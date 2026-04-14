---
title: "Hermes Agent 完全指南：原理、安装、使用与小技巧"
date: 2026-04-14 12:00:00 +0800
author: latte
categories: [AI, 工具评测]
tags: [Hermes Agent, AI Agent, 开源工具, LLM, 自动化]
toc: true
---

> **项目地址**：[github.com/NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)  
> **当前版本**：v0.8.0（2026 年 4 月 8 日）  
> **许可证**：MIT  
> **GitHub Stars**：52,800+（截至 2026 年 4 月）

---

## 一、原理

### 1.1 设计目标与定位

Hermes Agent 是由 Nous Research 开发并开源的自主 AI 智能体框架，官方定位是 **"The agent that grows with you"**——一个随使用时间不断成长的 Agent。

它解决的核心问题是当前 AI Agent 框架的三大痛点：

**无跨会话记忆**：大多数 Agent 每次会话结束即遗忘，Hermes 通过四层持久化记忆体系解决这一问题。

**能力固化无法自我扩展**：传统 Agent 只能使用预设工具，Hermes 内置学习闭环，能从执行经验中自主创建可复用技能（Skill），越用越强。

**与单一模型/平台深度绑定**：Hermes 支持 OpenRouter（200+ 模型）、Anthropic、OpenAI、Gemini、本地 LLM 等，通过 `hermes model` 一键切换，不锁定任何提供商。

与同类项目的核心区别：

| 特性 | Hermes Agent | AutoGPT | CrewAI |
|------|-------------|---------|--------|
| 自学习循环 | 是（原生内置） | 否 | 否 |
| 跨会话持久记忆 | 四层记忆体系 | 有限 | 无 |
| 消息平台网关 | 6 种 IM 平台 | 否 | 否 |
| 执行后端 | 6 种 | 本地为主 | 本地为主 |
| 安装方式 | 一行 curl | pip install | pip install |

### 1.2 底层架构

Hermes Agent 采用分层模块化架构，各层职责清晰：

```
用户接口层
  ├── CLI (hermes)
  └── Gateway (Telegram / Discord / Slack / ...)

Agent 核心层
  ├── AgentLoop（推理循环）
  ├── Memory（记忆管理）
  ├── Skills（技能系统）
  └── Cron（定时调度）

工具系统层
  ├── Terminal / File / Web / Browser
  ├── Vision / Delegate / MCP
  └── Memory（记忆工具）

执行后端层
  ├── Local / Docker / SSH
  └── Modal / Daytona / Singularity

模型提供商层
  └── OpenRouter / Anthropic / OpenAI / Gemini / 本地 vLLM / ...
```

代码仓库结构：

```
hermes-agent/
├── hermes_cli/       # CLI 入口层：命令注册、TUI 界面、配置管理
├── agent/            # 智能体核心：AgentLoop、消息处理、流式输出
├── tools/            # 工具系统：40+ 工具实现
│   └── environments/ # 终端后端：local/docker/ssh/modal/daytona
├── gateway/          # 消息网关：多平台集成
│   └── platforms/    # 平台适配器：Telegram/Discord/Slack/...
├── cron/             # 定时调度系统
├── skills/           # 内置技能库（40+）
├── optional-skills/  # 可选技能库
└── acp_adapter/      # ACP 协议适配器（VS Code/Zed/JetBrains）
```

### 1.3 推理机制：AgentLoop

Hermes Agent 的推理范式是**工具调用驱动的 ReAct 循环**（Reason + Act），每轮会话的生命周期如下：

```
消息到达
  → 生成 task_id（隔离会话状态）
  → 构建系统提示词（注入 MEMORY.md + USER.md 快照 + 技能文档 + 上下文文件）
  → 上下文压缩预检（防止 Token 溢出）
  → 调用 LLM（流式输出）
  → 解析工具调用请求
  → 并行执行工具
  → 将工具结果追加回对话历史
  → 再次调用 LLM（下一轮推理）
  → 直到 LLM 返回最终文本
  → 会话写入 SQLite（FTS5 索引）
  → 通过网关回传响应
```

**上下文压缩机制**：当对话历史接近上下文窗口上限时，辅助模型会将中间轮次做摘要压缩至 3575 字符限制内，原始对话血统链保留在 SQLite 中，可追溯。

**提示词缓存优化**：系统提示来自稳定数据源（MEMORY.md、USER.md 在会话中不热更新），前缀在多轮对话中基本不变，大多数服务商会自动缓存，降低延迟和成本。三种情况会击穿缓存：会话中途切模型、改记忆文件、改上下文文件。

### 1.4 工具调用机制

Hermes Agent 内置 **40+ 工具**，按功能分为多个工具集（Toolset）：

| 工具集 | 包含工具 | 说明 |
|--------|---------|------|
| `terminal` | `terminal`、`process` | 命令执行与进程管理 |
| `file` | `read_file`、`write_file`、`patch`、`search_files` | 文件读写与搜索 |
| `web` | `web_search`、`web_extract` | 网络搜索与内容提取 |
| `browser` | `browser_navigate`、`browser_click` 等 10 个工具 | 浏览器自动化 |
| `vision` | `vision_analyze` | 图像分析 |
| `image_gen` | `image_generate` | 图像生成 |
| `skills` | `skills_list`、`skill_view`、`skill_manage` | 技能管理 |
| `memory` | `memory` | 持久化记忆读写 |
| `delegate` | `delegate_task` | 子 Agent 委托 |
| `code` | `execute_code` | 代码执行（跨后端支持） |
| `todo` | `todo` | 任务列表管理 |
| `mcp` | 任意 MCP 工具 | MCP 协议扩展 |

工具系统支持**条件激活**：每个工具可提供 `check_fn` 检测依赖是否满足（如 `send_message` 仅在 Gateway 运行时激活）。

**插件钩子**：Hermes 提供四个插件钩子供深度扩展：`pre_llm_call`、`post_llm_call`、`on_session_start`、`on_session_end`。

### 1.5 记忆管理：四层记忆体系

Hermes Agent 的记忆体系是其最核心的创新，分为四层，各司其职：

**第一层：提示词记忆（Prompt Memory）**

存储在 `~/.hermes/memories/MEMORY.md`（Agent 学到的环境/项目/工具信息）和 `~/.hermes/memories/USER.md`（用户偏好、沟通风格、工作习惯）。每次会话开始时自动注入系统提示，无需主动调取。两个文件合计字符上限为 **3575**，设计上刻意紧凑，强制精选而非无脑堆积。

重要细节：会话中对这两个文件的修改**要到下次会话才生效**（为了保持提示词缓存稳定）。

**第二层：会话检索（Session Search）**

所有历史会话存入 SQLite，使用 **FTS5 全文索引**。Agent 检索历史时不会把整段旧对话塞进上下文，而是先检索、再通过 LLM 做摘要，只注入与当前任务相关的内容。

**第三层：技能（Skills）**

存储在 `~/.hermes/skills/` 下的 Markdown 文件，负责**过程记忆**（该怎么做）。加载策略是**渐进式披露**：默认只加载技能名和简介，完整内容只在 Agent 判断当前任务需要时才载入。因此 200 个技能的 Agent 与 40 个技能的上下文开销基本持平。

**第四层：Honcho 用户建模（可选）**

跨会话默默给用户画像，记录偏好、说话风格、专业领域，随时间持续更新。适合将 Hermes 作为日常私人助理的场景。

**学习闭环（Learning Loop）**

Hermes 的自我进化依赖一套全自动的学习闭环，包含四个核心模块：

1. **定时提醒机制**：会话运行的固定间隔里，Agent 自主判断近期操作中有无值得存入记忆的内容，无需用户干预。
2. **自主生成技能**：任务完成后，若调用了 5 次及以上工具、从错误中成功恢复、用户给过修正指导或走通了一套不直观的有效流程，Agent 自动生成技能文件。
3. **技能自我进化**：Agent 在后续使用中发现更好路径时，通过 `skill_manage` 工具以 **patch（补丁）** 方式更新技能，省 Token 且不破坏原有有效内容。
4. **FTS5 会话检索 + LLM 摘要**：情景记忆与过程记忆分开存储，互不干扰。

---

## 二、安装

### 2.1 环境依赖

| 依赖项 | 要求 |
|--------|------|
| 操作系统 | Linux（主流发行版）、macOS、Windows WSL2、Android Termux |
| Python | 3.11（安装脚本通过 uv 自动管理） |
| 内存 | 建议 4GB+（本地运行 Hermes 模型需 16GB+） |
| 网络 | 需访问 GitHub 及选定的 LLM 提供商 API |

> ⚠️ 原生 Windows 支持仍处于实验阶段，请安装 WSL2 后在其中运行。

### 2.2 标准安装（推荐）

一行命令完成全部安装，无需 sudo 权限：

```bash
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash

# 安装完成后重载 shell
source ~/.bashrc  # 或 source ~/.zshrc

# 验证安装
hermes --version
# 预期输出：hermes v0.8.0 (v2026.4.8)
```

### 2.3 开发者安装

```bash
git clone https://github.com/NousResearch/hermes-agent.git
cd hermes-agent

curl -LsSf https://astral.sh/uv/install.sh | sh
uv venv venv --python 3.11
source venv/bin/activate
uv pip install -e ".[all,dev]"

python -m pytest tests/ -q
```

### 2.4 网络受限环境（手动安装）

```bash
git clone https://mirror.ghproxy.com/https://github.com/NousResearch/hermes-agent.git
cd hermes-agent
pip install -r requirements.txt
python -m hermes
```

### 2.5 配置文件说明

主配置文件位于 `~/.hermes/config.yaml`。

**模型配置**：

```yaml
model:
  default: "anthropic/claude-opus-4.6"
  provider: "auto"
  base_url: "https://openrouter.ai/api/v1"
```

**使用本地模型（Ollama 示例）**：

```yaml
model:
  provider: "ollama"
  base_url: "http://localhost:11434/v1"
  default: "qwen2.5-coder:32b"
```

**智能模型路由**：

```yaml
smart_model_routing:
  enabled: true
  max_simple_chars: 160
  max_simple_words: 28
  cheap_model:
    provider: openrouter
    model: google/gemini-2.5-flash
```

**终端后端配置（Docker 示例）**：

```yaml
terminal:
  backend: "docker"
  cwd: "/workspace"
  timeout: 180
  docker_image: "nikolaik/python-nodejs:python3.11-nodejs20"
  container_cpu: 2
  container_memory: 8192
  container_persistent: true
```

主要终端配置参数：

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `backend` | string | `local` | 执行后端类型 |
| `cwd` | string | `.` | 工作目录 |
| `timeout` | int | `180` | 单条命令超时（秒） |
| `lifetime_seconds` | int | `300` | 终端会话生命周期（秒） |
| `container_cpu` | int | `1` | 容器 CPU 核数 |
| `container_memory` | int | `5120` | 容器内存（MB） |
| `container_persistent` | bool | `true` | 容器文件系统是否跨会话持久化 |

### 2.6 常见安装报错及解决方案

**Q：安装后找不到 hermes 命令**

```bash
echo $PATH | grep local
export PATH="$HOME/.local/bin:$PATH"
```

**Q：Python 版本不满足要求**

```bash
pyenv install 3.11
pyenv local 3.11
```

**Q：国内网络无法访问 GitHub**

```bash
git clone https://mirror.ghproxy.com/https://github.com/NousResearch/hermes-agent.git
cd hermes-agent
pip install -r requirements.txt
```

---

## 三、使用

### 3.1 快速上手

```bash
hermes setup   # 一键运行配置向导
hermes model   # 单独配置模型提供商
hermes         # 启动 Agent
```

### 3.2 核心 CLI 命令

```bash
hermes                    # 启动交互式 CLI
hermes setup              # 运行配置向导
hermes model              # 切换 LLM 模型/提供商
hermes tools              # 启用/禁用工具模块
hermes config set <k> <v> # 修改单个配置项
hermes config list        # 查看当前配置
hermes doctor             # 一键诊断配置和依赖问题
hermes update             # 更新到最新版本
hermes gateway setup      # 配置消息网关
hermes gateway            # 启动消息网关
hermes gateway install    # 安装为系统服务（systemd）
hermes skills list        # 浏览可用技能
hermes skills install <n> # 安装社区技能
hermes migrate --from openclaw  # 从 OpenClaw 迁移
```

会话内斜杠命令：

```
/compress   手动触发上下文压缩
/stop       中断当前任务
/skills     浏览和调用技能
/cron       管理定时任务
```

### 3.3 典型使用场景

**场景一：代码开发辅助**

Agent 会自动调用 `read_file`、`search_files`、`terminal` 等工具扫描代码，生成分析报告。若这是首次处理此类任务，完成后会自动生成"代码安全审查"技能文件，下次遇到类似任务直接复用。

**场景二：定时自动化任务**

```bash
hermes schedule "每天早上 8 点，汇总我的邮件并发送到 Telegram"
hermes schedule list
```

**场景三：多平台消息接入**

```bash
hermes gateway setup       # 配置 Telegram Bot Token
hermes gateway install     # 安装为 systemd 服务
```

配置完成后，可在 Telegram 中直接与 Agent 对话，会话状态与 CLI 共享。

**场景四：并行子 Agent 工作流**

Agent 会通过 `delegate_task` 工具生成多个隔离的子 Agent 并行执行，通过 RPC 将多步骤流水线压缩为零上下文消耗的操作。

### 3.4 自定义工具开发与注册

**方式一：通过 MCP 协议扩展**

```yaml
mcp:
  servers:
    - name: my-tools
      command: "python"
      args: ["/path/to/my_mcp_server.py"]
```

**方式二：通过插件钩子注入逻辑**

```python
# my_plugin.py
def pre_llm_call(messages, config):
    return messages, config

def on_session_start(session_id, config):
    pass
```

```yaml
plugins:
  - path: "/path/to/my_plugin.py"
```

---

## 四、小技巧

### 4.1 提示词工程

**技巧一：在 MEMORY.md 中建立项目上下文**

不要每次会话都重新解释项目背景，主动引导 Agent 将关键信息写入 MEMORY.md：

```
请把这个项目的技术栈、目录结构和核心约定记录到你的记忆中，以后不用我每次重复说明
```

**技巧二：用 USER.md 固化个人偏好**

```
我偏好简洁的代码注释，不喜欢冗长的解释。回复时直接给结论，不要铺垫。请把这些偏好记录下来。
```

**技巧三：为复杂任务提供结构化指令**

```
请完成以下任务：
1. 读取 data.csv，过滤掉 status=inactive 的行
2. 按 category 分组统计 revenue 总和
3. 生成柱状图保存为 output.png
验收标准：output.png 存在且文件大小 > 10KB
```

**技巧四：利用 /compress 主动管理上下文**

长会话中，当 Agent 开始"忘事"时，主动执行 `/compress` 压缩历史，保留关键信息同时释放上下文空间。

**技巧五：通过 SOUL.md 定制 Agent 人格**

```markdown
# ~/.hermes/memories/SOUL.md
你是一名专注于 Java 后端开发的技术专家，熟悉 Spring Boot、MyBatis 和分布式系统。
回答问题时优先考虑生产环境的稳定性和可维护性，对性能优化持保守态度。
```

### 4.2 工具/插件组合技巧

**技巧一：web + terminal 组合实现自动化研究**

Agent 会自动组合 `web_search`、`web_extract`、`write_file` 完成从网络搜索到本地保存的全流程。

**技巧二：browser + vision 组合处理视觉任务**

指令 Agent 打开某页面，截图后用视觉能力分析数据趋势或 UI 异常，无需手动截图上传。

**技巧三：delegate + terminal 实现并行计算**

对于耗时的独立任务，主动提示 Agent 使用子 Agent 并行处理，显著缩短等待时间。

**技巧四：cron + gateway 实现无人值守监控**

通过定时任务 + 消息网关组合，实现：每小时检查服务器磁盘使用率，超阈值自动发 Telegram 提醒。

**技巧五：善用技能市场（Skills Hub）**

```bash
hermes skills list
hermes skills install github-workflow
hermes skills tap https://github.com/your-org/custom-skills
```

### 4.3 调试与排查技巧

**技巧一：开启详细日志**

```bash
hermes config set log.level debug
tail -f ~/.hermes/logs/hermes.log
```

**技巧二：使用 hermes doctor 快速诊断**

```bash
hermes doctor
```

输出包括：配置文件有效性、LLM 提供商连通性、工具依赖检查、网关状态等。

**技巧三：定位 Agent 推理链异常**

1. **查看工具调用历史**：确认 Agent 是否正确理解了任务。
2. **检查 MEMORY.md 是否有干扰信息**：手动编辑 `~/.hermes/memories/MEMORY.md` 删除过时内容。
3. **检查技能文件是否有错误步骤**：查看 `~/.hermes/skills/` 下相关技能文件，确认步骤描述是否准确。

**技巧四：常见 Bad Case 排查思路**

| 现象 | 可能原因 | 排查方法 |
|------|---------|---------|
| Agent 反复执行同一操作 | 工具返回结果未被正确解析 | 检查工具输出格式，确认 LLM 能理解 |
| Agent 忽略之前的约定 | MEMORY.md 未写入或被压缩丢失 | 检查 `~/.hermes/memories/MEMORY.md` 内容 |
| 技能触发不准确 | 技能 description 不够精确 | 编辑技能文件，优化 description 字段 |
| 上下文溢出导致任务中断 | 会话历史过长 | 主动执行 `/compress` 或拆分任务 |
| 网关消息延迟 | 网关进程未以服务方式运行 | 执行 `hermes gateway install` 安装为系统服务 |

**技巧五：会话回放与历史检索**

```
请搜索我们上次讨论 Docker 部署配置的会话，把关键配置参数告诉我
```

Agent 会通过 `session_search` 工具检索历史会话，通过 LLM 摘要提取相关内容。

### 4.4 其他实用技巧

**技巧一：成本控制——善用智能模型路由**

```yaml
smart_model_routing:
  enabled: true
  max_simple_chars: 160
  cheap_model:
    provider: openrouter
    model: google/gemini-2.5-flash
```

**技巧二：性能优化——选择合适的执行后端**

- **local**：日常开发调试，延迟最低
- **docker**：需要隔离环境，避免污染本机
- **modal / ssh**：计算密集型任务，利用远程算力
- **singularity**：HPC 集群场景

**技巧三：多 Agent 协作——通过 delegate_task 构建工作流**

可以创建包含多个子 Agent 的流水线工作流：子 Agent 1 检查代码风格，子 Agent 2 分析安全漏洞，子 Agent 3 评估性能瓶颈，最后汇总报告。

**技巧四：从 OpenClaw 平滑迁移**

```bash
hermes migrate --from openclaw
```

一键迁移所有配置（SOUL.md、记忆、技能、API Key）。

**技巧五：利用 hermes-function-calling 子框架做代码集成**

```python
from hermes_function_calling import HermesAgent

agent = HermesAgent(
    model_path="NousResearch/Hermes-2-Pro-Llama-3-8B",
    chat_template="chatml",
    max_depth=5
)
response = agent.run("帮我查询北京今天的天气")
```

---

## 延伸资源

- 官方仓库：[github.com/NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)
- 官方中文站：[hermes-agent.org/zh](https://hermes-agent.org/zh/)
- 中文社区文档：[hermesagent.org.cn](https://hermesagent.org.cn/)
- 函数调用框架：[github.com/NousResearch/hermes-function-calling](https://github.com/NousResearch/hermes-function-calling)
- 技能市场：[agentskills.io](https://agentskills.io)
- Hermes 3 技术报告：[arxiv.org/abs/2408.11857](https://arxiv.org/abs/2408.11857)
- 模型下载：[huggingface.co/NousResearch](https://huggingface.co/NousResearch)
