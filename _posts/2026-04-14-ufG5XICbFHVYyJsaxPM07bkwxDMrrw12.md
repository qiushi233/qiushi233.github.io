---
title: "Hermes Agent：唯一能自我进化的 AI 智能体完全指南"
date: 2026-04-14 18:30:00 +0800
author: latte
categories: [AI工具]
tags: [Hermes Agent, AI Agent, 自我进化, 开源]
img_path: /assets/img/posts/2026-04-14/
toc: true
---

# Hermes Agent：唯一能自我进化的 AI 智能体完全指南

## 前言

在 AI 技术飞速发展的今天，我们见证了无数 AI Agent 的诞生与消亡。大多数 AI Agent 都是"工具箱"式的——你给它什么技能，它就用什么。但 Hermes Agent 不一样。

它是目前全球**唯一一个内置学习闭环**的开源 AI Agent，能够从自己的经验中创建技能、在使用中不断改进，并主动记住重要信息。

**核心理念**："The agent that grows with you"（与你共同成长的 Agent）

---

## 一、Hermes Agent 是什么？

### 1.1 项目概述

Hermes Agent 是由 **Nous Research**（知名 AI 研究实验室，曾推出 Hermes、Nomos 等知名模型）开发的开源自主进化 AI 智能体。

- **开源协议**：MIT License
- **GitHub Stars**：35.7k+（持续增长）
- **最新版本**：v0.8.0（2026 年 4 月 8 日更新）
- **主要语言**：Python（占比 93.6%）

### 1.2 核心定位

与普通 AI 助手不同，Hermes Agent 具备三大核心能力：

| 能力 | 说明 |
|------|------|
| **自我改进** | 从经验中创建技能，在使用中持续优化 |
| **持久记忆** | 跨会话记住你的偏好、项目上下文和环境信息 |
| **全平台覆盖** | CLI、Telegram、Discord、WhatsApp、Signal、飞书等 15+ 平台 |

### 1.3 为什么它特别？

**普通 AI Agent：**
- 每次对话都是从零开始
- 没有持久记忆，7 秒就忘
- 能力固定，不会学习
- 需要手动配置所有技能

**Hermes Agent：**
- ✅ 从复杂任务中自动创建可复用技能
- ✅ 跨会话记住你的工作风格和偏好
- ✅ 技能随使用不断改进
- ✅ 兼容 agentskills.io 开放标准，可自主搜索和安装技能

---

## 二、核心原理与架构

### 2.1 自进化学习循环

这是 Hermes Agent 最核心的创新，也是它与其他 Agent 最大的区别。

**循环流程：**

```
执行任务 → 自评估复盘 → 提取技能 → 优化存储 → 下次复用
```

**具体过程：**

1. **用户下达任务**
2. **Agent 自主规划执行**
3. **完成 15 次工具调用或一个复杂任务**
4. **自动触发自评估**
5. **将成功流程转化为 Markdown 技能**
6. **存储到技能库供后续复用**

**实战案例：**

```
你："帮我分析这个复杂的项目文档"
↓
Hermes："我将文档分为：架构设计、API 文档、部署指南、最佳实践"
↓
Hermes："正在分析架构设计...发现 3 个核心模块...识别出 5 个关键依赖..."
↓
你："你学会了吗？"
↓
Hermes："我已经从这个任务中提取了一个技能：'复杂技术文档分析流程'
这个技能包含：
1. 文档结构识别
2. 分层分析策略
3. 关键信息提取
4. 问题识别和澄清
5. 综合总结方法
技能已保存到: ~/.hermes/skills/document_analysis.md"
```

### 2.2 分层持久记忆系统

Hermes Agent 采用四层记忆架构，解决传统 AI"健忘"的问题：

| 层级 | 文件 | 容量 | 内容 |
|------|------|------|------|
| **热记忆** | System Prompt | 动态 | 当前会话系统提示，保障即时连贯 |
| **温记忆** | SQLite + FTS5 | 无限 | 跨会话历史、操作习惯，支持全文检索 |
| **冷记忆** | MEMORY.md | 2,200 字符 | Agent 技能总结 |
| **冷记忆** | USER.md | 1,375 字符 | 用户深度画像 |

**特点：**

- 会话搜索基于 FTS5 全文搜索 + LLM 自动总结
- 支持会话血缘追踪（压缩后的父子关系）
- 7 种可插拔外部记忆提供者（Honcho、Hindsight、Mem0 等）

### 2.3 技能系统

技能是 Hermes Agent 的程序化记忆——当智能体解决复杂任务（通常涉及 5+ 次工具调用）后，会自主创建结构化的 Markdown 技能文档。

**技能目录结构：**

```
~/.hermes/skills/
├── devops/deploy-k8s/
│   └── SKILL.md
└── mlops/axolotl/
    └── SKILL.md
```

**技能特点：**

- 兼容 agentskills.io 开放标准
- 渐进式披露模式，最小化 token 使用
- 可作为斜杠命令直接调用：`/axolotl help me fine-tune Llama 3`
- 支持外部技能目录（只读共享）

### 2.4 多平台网关系统

Hermes Agent 支持单一 gateway 进程连接 15+ 消息平台：

- **CLI**：交互式终端界面
- **Telegram**：语音转文字，跨平台对话
- **Discord**
- **Slack**
- **WhatsApp**
- **Signal**
- **Email**
- **飞书**
- **企业微信**
- **Matrix**
- **Mattermost**

**特点：**

- 跨平台对话连续性
- 在 Telegram/Slack 上支持原生按钮进行危险命令审批
- 语音备忘录转写

### 2.5 六种终端后端

Hermes Agent 支持六种命令执行方式，适应不同场景：

| 后端 | 说明 | 适用场景 |
|------|------|----------|
| **Local** | 直接在笔记本执行 | 日常开发 |
| **Docker** | 容器隔离执行，安全沙箱 | 生产环境 |
| **SSH** | 远程服务器执行 | 服务器管理 |
| **Daytona** | 无服务器持久化 | 成本敏感场景 |
| **Singularity** | HPC 环境容器 | 高性能计算 |
| **Modal** | 无服务器，按需唤醒 | 动态扩缩容 |

---

## 三、安装指南

### 3.1 系统要求

| 平台 | 支持状态 |
|------|----------|
| **Linux** | ✅ 完整支持 |
| **macOS** | ✅ 完整支持 |
| **WSL2** | ✅ 完整支持 |
| **Windows 原生** | ❌ 不支持 |

**注意：**Windows 用户必须使用 WSL2（推荐 Ubuntu 22.04）

### 3.2 前置依赖

安装脚本会自动处理所有依赖，但建议先检查：

```bash
# 检查 Git 版本
git --version

# 如果未安装：
# Ubuntu/Debian
sudo apt install git
# macOS（通过 Homebrew）
brew install git
```

### 3.3 一键安装（推荐）

**Linux / macOS / WSL2：**

```bash
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
```

安装完成后重新加载 Shell：

```bash
# Bash 用户
source ~/.bashrc

# Zsh 用户
source ~/.zshrc
```

### 3.4 手动安装（进阶用户）

#### 步骤 1：安装 uv 包管理器

```bash
# Linux/macOS
curl -LsSf https://astral.sh/uv/install.sh | sh

# 或通过 pip
pip install uv
```

#### 步骤 2：克隆仓库并创建虚拟环境

```bash
# 克隆仓库（包含子模块）
git clone --recurse-submodules https://github.com/NousResearch/hermes-agent.git
cd hermes-agent

# 创建 Python 3.11 虚拟环境
uv venv venv --python 3.11
source venv/bin/activate
```

#### 步骤 3：安装依赖

```bash
# 完整安装（推荐）
uv pip install -e ".[all]"

# 或仅安装核心功能
uv pip install -e "."
```

**可选 extras：**

```bash
# 语音输入/输出支持
uv pip install "hermes-agent[voice]"

# 消息平台集成
uv pip install "hermes-agent[messaging]"

# 浏览器自动化
uv pip install "hermes-agent[browser]"

# 向量数据库
uv pip install "hermes-agent[vector]"

# 完整功能
uv pip install "hermes-agent[all]"
```

#### 步骤 4：创建配置目录

```bash
mkdir -p ~/.hermes/{cron,sessions,logs,memories,skills,pairing,hooks}
```

#### 步骤 5：配置环境变量

```bash
# 创建 .env 文件
touch ~/.hermes/.env

# 编辑文件，添加 API Key
nano ~/.hermes/.env
```

**.env 文件示例：**

```bash
# OpenRouter（推荐，支持 200+ 模型）
OPENROUTER_API_KEY=sk-or-v1-your-key-here

# OpenAI
OPENAI_API_KEY=sk-your-openai-key

# Anthropic (Claude)
ANTHROPIC_API_KEY=sk-ant-your-anthropic-key

# Google Gemini
GOOGLE_API_KEY=your-google-key

# 本地 Ollama（无需 API Key）
# 确保 Ollama 服务运行在 localhost:11434
```

#### 步骤 6：验证安装

```bash
# 查看版本
hermes --version

# 诊断检查
hermes doctor
```

### 3.5 Docker 部署

**拉取镜像：**

```bash
docker pull nousresearch/hermes-agent:latest
```

**运行容器：**

```bash
docker run -it \
  -v ~/.hermes:/root/.hermes \
  -e OPENROUTER_API_KEY=your_key_here \
  nousresearch/hermes-agent:latest
```

**Docker Compose 配置：**

```yaml
version: "3.9"
services:
  hermes:
    image: nousresearch/hermes-agent:latest
    volumes:
      - ~/.hermes:/root/.hermes
    environment:
      - OPENROUTER_API_KEY=${OPENROUTER_API_KEY}
      - ANTHROPIC_API_KEY=${ANTHROPIC_API_KEY}
    stdin_open: true
    tty: true
    restart: unless-stopped
```

---

## 四、配置与初始化

### 4.1 运行设置向导

```bash
# 完整配置向导
hermes setup

# 选择模型提供商
hermes model

# 配置工具
hermes tools

# 查看当前配置
hermes config
```

### 4.2 配置模型提供商

**支持的提供商：**

| 提供商 | 环境变量 | 说明 |
|--------|----------|------|
| **OpenRouter** | `OPENROUTER_API_KEY` | 支持 200+ 模型 |
| **OpenAI** | `OPENAI_API_KEY` | GPT 系列模型 |
| **Anthropic** | `ANTHROPIC_API_KEY` | Claude 系列 |
| **Google** | `GOOGLE_API_KEY` | Gemini 系列 |
| **Kimi** | `KIMI_API_KEY` | 月之暗面 |
| **MiniMax** | `MINIMAX_API_KEY` | MiniMax 模型 |
| **本地 Ollama** | 无需 Key | 本地运行，完全免费 |

**切换模型：**

```bash
# 交互式切换
hermes model

# 直接指定模型启动
hermes chat --model gpt-4o
hermes chat --model claude-3-opus
hermes chat --model ollama/llama3.1
```

### 4.3 配置 SOUL.md 人格设定（可选）

通过 `~/.hermes/SOUL.md` 定义 Agent 的全局人格：

```markdown
# ~/.hermes/SOUL.md

## 身份
你是一位经验丰富的软件工程师和 AI 助手，名为 Hermes。

## 性格特点
- 专业但不失亲和
- 注重代码质量和最佳实践
- 善于用类比解释复杂概念
- 主动提出改进建议

## 沟通风格
- 回答简洁明了，避免冗余
- 代码示例配有详细注释
- 重要信息使用列表或表格呈现
- 技术术语配合通俗解释

## 工作原则
1. 安全第一：不执行危险操作
2. 验证优先：关键操作需确认
3. 效率导向：优先选择高效方案
4. 学习导向：鼓励用户理解原理
```

### 4.4 终端沙箱配置

**Docker 沙箱（推荐生产环境）：**

```bash
# 配置使用 Docker 后端
hermes config set terminal.backend docker
```

```yaml
# ~/.hermes/config.yaml
terminal:
  backend: docker
  docker:
    image: "python:3.11-slim"
    timeout: 300
```

**SSH 远程终端：**

```yaml
terminal:
  backend: ssh
  ssh:
    host: "your-server.com"
    user: "hermes"
    port: 22
    key_path: "~/.ssh/hermes_key"
    timeout: 300
```

---

## 五、快速开始

### 5.1 启动交互式对话

```bash
hermes
```

### 5.2 基础使用示例

```bash
# 启动对话
hermes

# 查看当前目录文件
>>> 列出当前目录的 Python 文件

# 写代码
>>> 写一个 Python 脚本计算斐波那契数列

# 恢复最近会话
hermes chat --continue

# 使用指定模型
hermes chat --model gpt-4o
```

### 5.3 常用斜杠命令

| 命令 | 功能 |
|------|------|
| `/new` | 开启新对话 |
| `/model provider:model` | 切换模型 |
| `/skills` | 查看已安装技能 |
| `/usage` | 查看 Token 使用情况 |
| `/retry` | 重试上一轮 |
| `/compress` | 压缩上下文 |

---

## 六、进阶功能

### 6.1 消息平台网关

**配置网关：**

```bash
# 设置消息平台
hermes gateway setup

# 启动网关
hermes gateway start

# 查看网关状态
hermes gateway status
```

**支持的平台：**

- Telegram
- Discord
- Slack
- WhatsApp
- Signal
- 飞书
- 企业微信
- Email
- Matrix
- Mattermost

### 6.2 技能管理

```bash
# 搜索技能
hermes skills search kubernetes

# 安装技能
hermes skills install openai/skills/k8s

# 查看技能详情
hermes skills inspect <skill>

# 检查上游更新
hermes skills check

# 更新技能
hermes skills update
```

### 6.3 定时任务（Cron）

使用自然语言设置定时任务：

```bash
# 在对话中输入：
Every morning at 9am, check Hacker News for AI news and send me a summary on Telegram.
```

Hermes 会自动设置通过网关运行的 cron 任务。

### 6.4 语音模式

```bash
# 安装语音支持
pip install "hermes-agent[voice]"

# CLI 中按 Ctrl+B 录音
/voice on  # 开启语音回复
```

### 6.5 MCP 集成

在 `~/.hermes/config.yaml` 中添加：

```yaml
mcp_servers:
  github:
    command: npx
    args: ["-y", "@modelcontextprotocol/server-github"]
    env:
      GITHUB_PERSONAL_ACCESS_TOKEN: "ghp_xxx"
```

### 6.6 从 OpenClaw 迁移

如果你之前使用 OpenClaw，可以无缝迁移：

```bash
# 交互式迁移
hermes claw migrate

# 预览迁移内容
hermes claw migrate --dry-run

# 仅迁移用户数据
hermes claw migrate --preset user-data

# 覆盖已有冲突
hermes claw migrate --overwrite
```

---

## 七、核心命令速查表

### 7.1 基础命令

| 命令 | 功能 |
|------|------|
| `hermes` | 启动交互式对话 |
| `hermes chat` | 启动对话 |
| `hermes chat --continue` | 恢复最近会话 |
| `hermes chat --model <model>` | 使用指定模型 |
| `hermes --version` | 查看版本 |
| `hermes --help` | 查看帮助 |

### 7.2 配置命令

| 命令 | 功能 |
|------|------|
| `hermes setup` | 运行完整设置向导 |
| `hermes model` | 选择 LLM 提供商和模型 |
| `hermes tools` | 配置工具 |
| `hermes config` | 查看当前配置 |
| `hermes config set <key> <value>` | 设置配置项 |
| `hermes doctor` | 运行诊断检查 |

### 7.3 网关命令

| 命令 | 功能 |
|------|------|
| `hermes gateway setup` | 配置消息平台 |
| `hermes gateway start` | 启动网关服务 |
| `hermes gateway stop` | 停止网关服务 |
| `hermes gateway status` | 查看网关状态 |

### 7.4 技能命令

| 命令 | 功能 |
|------|------|
| `hermes skills list` | 列出技能 |
| `hermes skills search <query>` | 搜索技能 |
| `hermes skills install <id>` | 安装技能 |
| `hermes skills inspect <skill>` | 安全审查技能 |

### 7.5 Profile 管理

```bash
# 列出所有配置文件
hermes profile list

# 创建新配置文件
hermes profile create work --clone

# 切换配置文件
hermes profile use work

# 查看当前配置文件
hermes profile show

# 删除配置文件
hermes profile delete work
```

---

## 八、常见问题排查

### 问题 1：hermes 命令找不到

**解决方案：**

```bash
source ~/.bashrc
# 或
source ~/.zshrc
```

### 问题 2：API Key 无效

**解决方案：**

```bash
hermes model  # 重新配置提供商
hermes doctor  # 运行诊断
```

### 问题 3：模型 401 错误

**解决方案：**

- 检查 API Key 是否正确
- 确认密钥格式无误（无前后空格）
- 检查 `.env` 文件是否存在

### 问题 4：飞书网关不回复

**解决方案：**

```bash
# 检查网关状态
hermes gateway status

# 查看日志
tail -f ~/.hermes/logs/gateway.log

# 临时放开所有用户（测试用）
hermes config set GATEWAY_ALLOW_ALL_USERS true
```

### 问题 5：依赖缺失

**解决方案：**

```bash
# 进入项目目录
cd ~/hermes-agent

# 补基础依赖
./venv/bin/python -m pip install pyyaml python-dotenv

# 安装项目依赖
./venv/bin/python -m pip install -e ".[all]"
```

---

## 九、实战案例

### 9.1 案例 1：云端 DevOps 运维助手

**痛点：** 个人开发者没有专职运维，服务器监控、日志清理、备份等任务繁琐且易忘

**Hermes 方案：**

1. 在 $5 的 VPS 上部署 Hermes
2. 配置 Telegram 网关
3. 设置定时任务：每天早上 9 点检查服务器状态

**使用方式：**

```
你：检查服务器磁盘空间并清理一周前的日志
```

Hermes 会自动调用 `df`、`find`、`rm` 等命令，将结果整理成 Markdown 表格发回群聊。

### 9.2 案例 2：个人知识库与写作教练

**痛点：** 研究者或写作者思路分散，不同时期的笔记和灵感难以关联

**Hermes 方案：**

1. 将 Hermes 指向你的笔记目录（如 Obsidian Vault）
2. 利用 RAG 检索所有相关片段
3. 作为审稿人提供深度对话

**使用方式：**

```
你：我之前关于 "Agent 记忆" 的笔记有哪些观点？
```

Hermes 会自动搜索所有相关片段，并生成摘要。

### 9.3 案例 3：自动化数据报告机器人

**痛点：** 产品经理需要每日手动拉取数据、制作图表、发送邮件

**Hermes 方案：**

1. 利用 Cron 调度功能
2. 设置每日 9:00 自动触发"生成日报"任务
3. Agent 自动调用 Python 脚本查询数据库，生成折线图
4. 通过 Email 或 Discord 频道发送报告

---

## 十、资源链接

### 10.1 官方资源

- **官方网站**：https://hermes-agent.nousresearch.com/
- **GitHub 仓库**：https://github.com/NousResearch/hermes-agent
- **官方文档**：https://hermes-agent.nousresearch.com/docs/
- **Skills Hub**：https://agentskills.io
- **Discord 社区**：https://discord.gg/NousResearch

### 10.2 社区项目

| 项目 | 说明 |
|------|------|
| **awesome-hermes-agent** | 精选技能、工具、集成和资源列表 |
| **hermes-workspace** | Hermes 原生工作空间（聊天、终端、技能管理器） |
| **mission-control** | 智能体编排仪表盘（舰队管理、任务调度、成本追踪） |

### 10.3 相关工具

- **OpenClaw**：https://github.com/openclaw/openclaw
- **Claude Code**：https://claude.ai/code
- **Auto-GPT**：https://github.com/Significant-Gravitas/Auto-GPT
- **CrewAI**：https://github.com/joaomdmoura/CrewAI

---

## 十一、总结

Hermes Agent 是目前开源 Agent 中**自我进化能力最强**的选择。

**核心优势：**

✅ **自我进化**：用得越多，能力越强
✅ **持久记忆**：跨会话记住你的偏好和工作风格
✅ **全平台覆盖**：CLI、Telegram、Discord、飞书等 15+ 平台
✅ **灵活部署**：从笔记本到 GPU 集群，$5 VPS 也能跑
✅ **模型无关**：不绑定任何提供商，随时切换
✅ **安全可控**：数据存储在自有设备，不上传第三方

**适合人群：**

- 个人开发者
- 研究人员
- 小型团队
- 注重隐私的用户
- 需要长期协作的用户

**不适合人群：**

- 追求开箱即用的用户
- 需要原生 Windows 支持的用户
- 大型企业/高并发场景
- 依赖中文文档的用户

如果你想让 AI 成为一个真正"懂你"的数字伙伴，Hermes Agent 是目前最优选择。

---

*文中信息基于公开资料整理，仅供参考，请以官方文档为准。*
