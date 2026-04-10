---
title: "Everything Claude Code 完全指南：打造你的 AI 编程超级助手"
date: 2026-04-10 13:30:00 +0800
author: latte
categories: [工具指南]
tags: [Claude Code, AI编程, 开发工具]
img_path: /assets/img/posts/2026-04-10/
toc: true
---

# Everything Claude Code 完全指南：打造你的 AI 编程超级助手

## 前言

如果你正在使用 **Claude Code**（Anthropic 的命令行 AI 编程助手），你可能会遇到以下痛点：

- **"金鱼记忆"**：对话变长或隔天再打开，AI 就忘了项目架构和决策
- **"随性派"问题**：需要反复强调编码规范，消耗 Token 和耐心
- **缺乏工程直觉**：原生 Claude Code 很强，但不懂你的项目习惯

**Everything Claude Code（ECC）** 就是解决这些问题的终极方案。

---

## 一、Everything Claude Code 是什么？

### 1.1 项目背景

Everything Claude Code 是由 **Anthropic 黑客马拉松冠军** Affaan Mustafa 开发的开源项目，经过 **10+ 个月**的实战打磨，在他构建真实产品（如 zenith.chat）的过程中不断优化而来。

**核心亮点：**

| 特性 | 说明 |
|------|------|
| ⭐ GitHub Stars | 22.7k+（持续增长中） |
| 🏆 实战验证 | Anthropic Hackathon 获奖项目 |
| 📦 组件丰富 | 13 个 Agents + 56 个 Skills + 32 个 Commands |
| 🔧 开箱即用 | 支持插件一键安装 |
| 🌍 跨平台 | Windows、macOS、Linux 全支持 |

### 1.2 核心价值

✅ **持久化记忆**：Hooks 自动保存项目上下文，告别重复解释

✅ **标准化工作流**：统一的编码规范和检查机制

✅ **自动化检查**：代码审查、安全检查、格式化自动化

✅ **持续学习**：从会话中自动提取模式，越用越智能

---

## 二、核心组件详解

Everything Claude Code 包含以下六大核心模块：

### 2.1 Agents（专业子代理）—— 13 个

将复杂任务委托给专业子代理，每个都有明确的职责。

| 代理名称 | 功能 | 使用场景 |
|----------|------|----------|
| **planner** | 功能实现规划 | 复杂功能开发、重构 |
| **architect** | 系统架构设计 | 架构决策、系统设计 |
| **tdd-guide** | 测试驱动开发指导 | 新功能、Bug 修复 |
| **code-reviewer** | 代码质量审查 | 写完代码后立即审查 |
| **security-reviewer** | 安全漏洞检测 | 提交前、敏感代码 |
| **build-error-resolver** | 修复构建错误 | 构建失败时 |
| **e2e-runner** | E2E 测试执行 | 关键用户流程测试 |
| **refactor-cleaner** | 死代码清理 | 代码维护 |
| **doc-updater** | 文档同步更新 | 更新文档 |
| **go-reviewer** | Go 代码审查 | Go 项目专用 |
| **go-build-resolver** | Go 构建错误修复 | Go 项目专用 |
| **python-reviewer** | Python 代码审查 | Python 项目专用 |
| **database-reviewer** | 数据库/Supabase 审查 | 数据库操作 |

**使用示例：**

```bash
# 规划新功能
/plan "添加用户认证系统，支持 OAuth2"

# 测试驱动开发
/tdd

# 代码审查
/code-review

# 安全漏洞扫描
/security-scan
```

### 2.2 Skills（技能库）—— 56+ 个

可复用的工作流定义，涵盖多个领域：

**通用开发：**
- `coding-standards` — 语言最佳实践
- `tdd-workflow` — TDD 方法论
- `security-review` — 安全检查清单
- `api-design` — REST API 设计
- `deployment-patterns` — CI/CD、Docker、健康检查

**前端：**
- `frontend-patterns` — React、Next.js 模式
- `e2e-testing` — Playwright E2E 测试

**后端 & 数据库：**
- `backend-patterns` — API、数据库、缓存模式
- `postgres-patterns` — PostgreSQL 优化
- `docker-patterns` — Docker Compose、容器安全

**语言专项：**
- `django-patterns` / `django-security` / `django-tdd`
- `springboot-patterns` / `springboot-security`
- `golang-patterns` / `golang-testing`

**内容创作：**
- `article-writing` — 无 AI 腔调的长文写作
- `content-engine` — 多平台社交内容工作流
- `market-research` — 市场调研

### 2.3 Commands（斜杠命令）—— 32 个

一键触发高频功能：

**开发流程：**
```bash
/plan              # 创建实现计划
/tdd               # TDD 工作流
/code-review       # 代码审查
/build-fix         # 修复构建错误
/e2e               # E2E 测试
/refactor-clean    # 死代码清理
/security-scan     # 安全漏洞扫描
```

**持续学习系统：**
```bash
/learn             # 从会话提取模式
/instinct-status   # 查看已学习的直觉
/evolve            # 将直觉聚类为技能
/skill-create      # 从 Git 历史生成技能
```

**Go 专项：**
```bash
/go-review         # Go 代码审查
/go-test           # Go TDD 工作流
/go-build          # 修复 Go 构建错误
```

### 2.4 Rules（编码规范）—— 始终遵循的准则

按语言分目录组织，确保代码一致性：

**通用规则（common/）：**
- `coding-style.md` — 编码风格（不可变性、文件组织）
- `security.md` — 安全指南（无硬编码密钥、输入验证）
- `testing.md` — 测试要求（TDD、80%覆盖率）
- `git-workflow.md` — Git 工作流（提交格式、PR 流程）

**语言专项：**
- `typescript/` — TypeScript/JavaScript 项目
- `python/` — Python 项目
- `golang/` — Go 项目
- `swift/` — Swift 项目

### 2.5 Hooks（钩子）—— 触发式自动化

基于事件触发的自动化脚本，解决"隐形痛点"：

**典型场景：**

```json
{
  "matcher": "tool == \"Edit\" && tool_input.file_path matches \"\\.(ts|tsx|js|jsx)$\"",
  "hooks": [{
    "type": "command",
    "command": "npx prettier --write \"$file_path\""
  }],
  "description": "编辑后自动格式化"
}
```

**内置 Hook：**
- **Dippy**：解决权限疲劳，自动管理工具权限
- **Parry**：对抗提示词注入风险
- **Git-Safety**：Git 操作安全检查
- **Pre-Commit**：提交前自动审查代码

### 2.6 MCP Configs（MCP 服务器配置）

预配置 14 个 MCP 服务器：

- **memory** — 持久化记忆（本地运行）
- **sequential-thinking** — 链式思考推理
- **filesystem** — 本地文件系统
- **github** — GitHub 仓库操作
- **supabase** — 数据库操作
- **vercel** — 部署服务
- **railway** — 云平台服务

---

## 三、安装指南

### 3.1 前置要求

- Claude Code CLI 版本：v2.1.0 或更高
- Git
- Node.js（用于 Hooks 脚本）

**检查环境：**

```bash
# 检查 Claude Code 版本
claude --version

# 检查 Git 版本
git --version

# 检查 Node.js 版本
node --version
```

### 3.2 安装方式一：插件安装（推荐）

这是最简单的方式，类似安装 Chrome 插件。

**第一步：添加市场源**

```bash
# 启动 Claude Code
claude

# 在 Claude 对话框中输入
/plugin marketplace add affaan-m/everything-claude-code
```

**第二步：安装插件**

```bash
/plugin install everything-claude-code@everything-claude-code
```

**第三步：选择安装范围**

会出现三个选项：

| 选项 | 说明 | 推荐人群 |
|------|------|----------|
| Install for you (user scope) | 安装到用户全局配置 | **推荐**个人开发者 |
| Install for collaborators (project scope) | 仅当前项目，推送到 Git | 团队协作 |
| Install for you (local scope) | 仅当前项目，不共享 | 秘密测试 |

**选择 "Install for you (user scope)"，按回车确认。**

### 3.3 安装方式二：手动安装

如果需要更精细的控制，可以手动安装。

**第一步：克隆仓库**

```bash
git clone https://github.com/affaan-m/everything-claude-code.git
cd everything-claude-code
```

**第二步：安装 Rules（必须手动）**

⚠️ 注意：Claude Code 插件系统不支持自动分发 Rules，必须手动安装。

```bash
# Windows
xcopy rules\* %USERPROFILE%\.claude\rules\ /E /I

# macOS/Linux
cp -r rules/* ~/.claude/rules/
```

**第三步：安装 Agents、Skills、Commands**

```bash
# 复制 Agents
cp agents/*.md ~/.claude/agents/

# 复制 Commands
cp commands/*.md ~/.claude/commands/

# 复制 Skills
cp -r skills/* ~/.claude/skills/
```

**第四步：配置 Hooks（可选）**

编辑 `~/.claude/settings.json`，添加：

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "tool == \"Edit\" && tool_input.file_path matches \"\\.(ts|tsx|js|jsx)$\"",
        "hooks": [{
          "type": "command",
          "command": "npx prettier --write \"$file_path\""
        }]
      }
    ]
  }
}
```

**第五步：配置 MCP 服务器（可选）**

编辑 `~/.claude.json`，添加：

```json
{
  "mcpServers": {
    "memory": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-memory"],
      "description": "持久化记忆（本地运行，无需网络）"
    },
    "sequential-thinking": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-sequential-thinking"],
      "description": "链式思考推理（本地运行）"
    }
  }
}
```

### 3.4 验证安装

重启 Claude Code，然后输入：

```bash
claude

# 测试核心命令
/plan

# 查看所有可用命令
/plugin list everything-claude-code@everything-claude-code
```

---

## 四、快速开始

### 4.1 初始化包管理器（推荐）

```bash
/setup-pm
```

这个命令会自动检测你电脑上已安装的包管理器（npm、pnpm、bun 等），并配置默认选项。

### 4.2 实战演示：开发一个贪吃蛇游戏

**第一步：规划**

```bash
/plan "使用 HTML5 和原生 JavaScript 写一个贪吃蛇游戏，界面要现代简洁，支持分数记录"
```

Claude 会输出一份详细的"项目设计书"，包括：

- 文件结构
- 核心功能拆解
- 技术细节
- UI 设计规范

**第二步：确认并执行**

如果对计划满意，输入 `yes` 确认，Claude 开始写代码。

**第三步：一键授权所有编辑**

当 Claude 问是否创建文件时，选择 "Yes, allow all edits during this session"，避免每个文件都要确认。

---

## 五、最佳实践

### 5.1 上下文窗口管理

⚠️ **关键提醒**：不要同时启用所有 MCP 服务器！

每个 MCP 工具描述都会消耗 200k 上下文窗口中的 Token，可能将可用上下文压缩到 ~70k。

**经验法则：**
- 配置 20-30 个 MCP
- 每个项目保持少于 10 个启用
- 保持少于 80 个活跃工具

**禁用不需要的 MCP：**

在项目 `.claude/settings.json` 中：

```json
{
  "disabledMcpServers": ["supabase", "railway", "vercel"]
}
```

### 5.2 Token 优化

在 `~/.claude/settings.json` 中添加：

```json
{
  "model": "sonnet",
  "env": {
    "MAX_THINKING_TOKENS": "10000",
    "CLAUDE_AUTOCOMPACT_PCT_OVERRIDE": "50",
    "CLAUDE_CODE_SUBAGENT_MODEL": "haiku"
  }
}
```

| 设置 | 默认值 | 推荐值 | 效果 |
|------|--------|--------|------|
| model | opus | sonnet | 约降低 60% 成本 |
| MAX_THINKING_TOKENS | 31,999 | 10,000 | 约降低 70% 隐藏推理成本 |
| CLAUDE_AUTOCOMPACT_PCT_OVERRIDE | 95 | 50 | 更早压缩，长会话质量更好 |

### 5.3 常用工作流

**开发新功能：**

```bash
# 1. 规划
/plan "Add user authentication with OAuth"

# 2. 测试驱动开发
/tdd

# 3. 代码审查
/code-review
```

**修复 Bug：**

```bash
# 1. 写失败测试
/tdd

# 2. 实现修复，验证测试通过

# 3. 审查
/code-review
```

**上线前检查：**

```bash
/security-scan      # 安全漏洞审计
/e2e                # E2E 测试
/test-coverage      # 验证 80%+ 覆盖率
```

---

## 六、进阶功能

### 6.1 持续学习系统 v2

从会话中自动提取模式，越用越智能：

```bash
/instinct-status    # 查看已学习的直觉及置信度
/instinct-import    # 导入他人的直觉
/instinct-export    # 导出自己的直觉分享给团队
/evolve             # 将相关直觉聚类为技能
```

### 6.2 Skill Creator（技能生成器）

从你的 Git 历史自动生成技能：

```bash
# 分析当前仓库
/skill-create

# 同时生成直觉
/skill-create --instincts
```

### 6.3 AgentShield（安全审计工具）

扫描配置文件中的安全问题：

```bash
# 快速扫描
npx ecc-agentshield scan

# 自动修复
npx ecc-agentshield scan --fix

# 深度分析
npx ecc-agentshield scan --opus --stream
```

---

## 七、常见问题

### Q1：插件安装后没有生效？

**检查步骤：**

1. 确认 Rules 是否已手动安装（插件不会自动安装 Rules）
2. 重启 Claude Code
3. 输入 `/plugin list` 查看已安装的插件

### Q2：上下文窗口变小了？

**原因：** 启用了太多 MCP 服务器。

**解决：** 在项目配置中禁用不需要的 MCP：

```json
{
  "disabledMcpServers": ["supabase", "railway", "vercel"]
}
```

### Q3：如何只安装部分组件？

**手动安装方式允许选择性安装：**

```bash
# 只安装 Rules
cp -r rules/common/* ~/.claude/rules/

# 只安装核心 Agents
cp agents/planner.md ~/.claude/agents/
cp agents/architect.md ~/.claude/agents/
```

---

## 八、总结

Everything Claude Code 是一个强大的 Claude Code 配置工具包，能够将原生的 Claude Code 转变为功能完善的开发环境。

**核心价值：**

✅ **实战验证** — 来自黑客松冠军的 10 个月日常使用经验

✅ **模块化设计** — 可以按需选择和定制组件

✅ **专业化分工** — 通过子代理实现任务委派

✅ **自动化增强** — 通过钩子实现工作流自动化

✅ **持续学习** — 能够从你的编程模式中学习

**GitHub 地址：** https://github.com/affaan-m/everything-claude-code

无论你是 Claude Code 新手还是资深用户，这个项目都能帮助你提升开发效率，建立更一致的代码质量标准。

---

*文中信息基于公开资料整理，仅供参考，请以官方文档为准。*
