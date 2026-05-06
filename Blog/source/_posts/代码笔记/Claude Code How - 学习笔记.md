---
title: Claude Code How - 学习笔记
date: 2026-04-14
categories:
  - 代码笔记
tags:
  - AI
  - ClaudeCode
archive: false
hide: false
---

> 本文档记录 Claude Code 的完整学习路径，按难度分级整理。

---

# 1. 初学者篇

---

## 1.1. Slash Commands（斜杠命令）

### 1.1.1 什么是 Slash Commands

Slash Commands 是在交互会话中通过 `/命令名` 触发的快捷方式，用于控制 Claude 的行为。

### 1.1.2 命令类型与常用内置命令速查

**命令类型：**

| 类型 | 来源 | 示例 |
|------|------|------|
| **内置命令** | Claude Code 提供 | `/help`, `/clear`, `/model` |
| **Skills** | 用户定义的 `SKILL.md` 文件 | `/optimize`, `/pr` |
| **Plugin 命令** | 安装的插件 | `/frontend-design:frontend-design` |
| **MCP 提示** | MCP 服务器 | `/mcp__github__list_prs` |

**常用内置命令速查：**

| 命令 | 用途 |
|------|------|
| `/help` | 显示帮助 |
| `/clear` | 清除对话 |
| `/model` | 切换模型（Sonnet/Opus/Haiku） |
| `/compact` | 压缩对话以节省上下文 |
| `/cost` | 显示 Token 使用统计 |
| `/branch` | 分支对话到新会话 |
| `/resume` | 恢复之前的会话 |
| `/rewind` | 回滚对话或代码 |
| `/init` | 初始化 CLAUDE.md |
| `/doctor` | 诊断安装问题 |
| `/fast` | 切换快速模式 |

### 1.1.3 创建自定义命令（Skills 方式）

推荐通过 Skills 方式创建自定义命令：

```bash
# 创建技能目录
mkdir -p .claude/skills/my-command
```

**文件模板：** `.claude/skills/my-command/SKILL.md`

```yaml
---
name: my-command
description: 命令描述，帮助 Claude 知道何时使用
allowed-tools: Bash(git *), Read
---

# 我的命令

## 上下文

- 当前分支: !`git branch --show-current`
- 相关文件: @package.json

## 指令

1. 第一步
2. 第二步，参数: $ARGUMENTS
3. 第三步
```

### 1.1.4 关键语法与示例

**关键语法：**

| 语法 | 用途 |
|------|------|
| `$ARGUMENTS` | 获取所有参数 |
| `$0`, `$1` | 获取第 0、1 个参数 |
| `` !`command` `` | 执行 shell 命令获取动态上下文 |
| `@file.txt` | 引用文件内容 |

**示例 — `/commit` 带上下文的 Git 提交：**

```yaml
---
allowed-tools: Bash(git add:*), Bash(git status:*), Bash(git commit:*), Bash(git diff:*)
argument-hint: [message]
description: Create a git commit with context
---

## Context

- Current git status: !`git status`
- Current git diff: !`git diff HEAD`
- Current branch: !`git branch --show-current`
- Recent commits: !`git log --oneline -10`

## Your task

Based on the above changes, create a single git commit.
If a message was provided via arguments, use it: $ARGUMENTS
```

**示例 — `/optimize` 代码优化分析：**

```yaml
---
description: Analyze code for performance issues and suggest optimizations
---

# Code Optimization

Review the provided code for the following issues in order of priority:

1. **Performance bottlenecks** - identify O(n²) operations
2. **Memory leaks** - find unreleased resources
3. **Algorithm improvements** - suggest better data structures
4. **Caching opportunities** - identify repeated computations
5. **Concurrency issues** - find race conditions
```

---

## 1.2. Memory（记忆系统）

### 1.2.1 什么是 Memory

Memory 让 Claude 能够 **跨会话和对话保持上下文**。不同于临时的上下文窗口，Memory 文件让你可以：
- 在团队间共享项目标准
- 存储个人开发偏好
- 维护目录特定的规则和配置

**Memory 命令速查：**

| 命令 | 用途 | 使用场景 |
|------|------|----------|
| `/init` | 初始化项目 Memory | 新项目启动，首次设置 CLAUDE.md |
| `/memory` | 在编辑器中编辑 Memory | 大量更新、重组、审查内容 |
| `@path/to/file` | 导入外部内容 | 引用现有文档到 CLAUDE.md |

### 1.2.2 Memory 层级架构

```
┌─────────────────────────────────────────────────────────────┐
│  优先级 1: Managed Policy (组织级策略)                       │
│  → /Library/Application Support/ClaudeCode/CLAUDE.md        │
├─────────────────────────────────────────────────────────────┤
│  优先级 2: Project Memory (项目级，团队共享)                 │
│  → ./CLAUDE.md 或 ./.claude/CLAUDE.md                       │
├─────────────────────────────────────────────────────────────┤
│  优先级 3: Project Rules (模块化规则)                        │
│  → ./.claude/rules/*.md                                     │
├─────────────────────────────────────────────────────────────┤
│  优先级 4: User Memory (用户级，个人偏好)                    │
│  → ~/.claude/CLAUDE.md                                      │
├─────────────────────────────────────────────────────────────┤
│  优先级 5: User Rules (用户级规则)                           │
│  → ~/.claude/rules/*.md                                     │
├─────────────────────────────────────────────────────────────┤
│  优先级 6: Local Project Memory (本地项目偏好)               │
│  → ./CLAUDE.local.md (加入 .gitignore)                      │
├─────────────────────────────────────────────────────────────┤
│  优先级 7: Auto Memory (Claude 自动记录)                     │
│  → ~/.claude/projects/<project>/memory/                     │
└─────────────────────────────────────────────────────────────┘
```

**Memory 位置详解：**

| 位置 | 范围 | 共享 | 最佳用途 |
|------|------|------|----------|
| `./CLAUDE.md` | 项目 | 团队 (Git) | 团队标准、共享架构 |
| `./.claude/rules/*.md` | 项目 | 团队 (Git) | 路径特定的模块化规则 |
| `~/.claude/CLAUDE.md` | 用户 | 个人 | 个人偏好（所有项目） |
| `./CLAUDE.local.md` | 项目本地 | 个人 | 项目特定的个人偏好 |
| `~/.claude/projects/.../memory/` | 自动 | 个人 | Claude 的自动学习笔记 |

### 1.2.3 Auto Memory 与模块化规则系统

**Auto Memory** 是 Claude 在会话中自动记录学习内容的地方：

```
~/.claude/projects/<project>/memory/
├── MEMORY.md              # 入口文件（启动时加载前 200 行）
├── debugging.md           # 主题文件（按需加载）
├── api-conventions.md     # 主题文件
└── testing-patterns.md    # 主题文件
```

控制 Auto Memory：

```bash
# 禁用 Auto Memory
CLAUDE_CODE_DISABLE_AUTO_MEMORY=1 claude

# 强制启用
CLAUDE_CODE_DISABLE_AUTO_MEMORY=0 claude
```

**模块化规则系统** — 在 `.claude/rules/` 目录下创建路径特定的规则：

```markdown
---
paths: src/api/**/*.ts
---

# API Development Rules

- All API endpoints must include input validation
- Use Zod for schema validation
- Document all parameters and response types
```

目录结构示例：

```
your-project/
├── .claude/
│   ├── CLAUDE.md
│   └── rules/
│       ├── code-style.md
│       ├── testing.md
│       └── api/
│           ├── conventions.md
│           └── validation.md
```

### 1.2.4 导入外部内容与最佳实践

使用 `@path/to/file` 语法引用现有文档：

```markdown
# Project Documentation
See @README.md for project overview
See @package.json for available npm commands
See @docs/architecture.md for system design
```

**最佳实践：**

| ✅ 应该做 | ❌ 不应该做 |
|----------|------------|
| 具体详细：`Use 2-space indentation` | 模糊：`Follow best practices` |
| 使用 `@path` 导入现有文档 | 复制已存在的内容 |
| 将项目 Memory 提交到 Git | 存储 API 密钥、密码等敏感信息 |
| 定期更新 Memory | 创建过长的 Memory 文件（<500 行） |
| 提供具体代码示例 | 过度组织目录结构 |

---

## 1.3. Skills（技能系统）

### 1.3.1 什么是 Skills 与核心优势

Skills 是 **可复用的、基于文件系统的能力模块**，用于扩展 Claude 的功能。它们将领域专业知识、工作流和最佳实践打包成可发现的组件，Claude 会在相关时自动使用。

| 优势 | 说明 |
|------|------|
| **专业化 Claude** | 为特定领域任务定制能力 |
| **减少重复** | 一次创建，跨会话自动使用 |
| **组合能力** | 组合多个 Skills 构建复杂工作流 |
| **规模化工作流** | 在多个项目和团队间复用 |
| **保持质量** | 将最佳实践直接嵌入工作流 |

### 1.3.2 渐进式披露架构

Skills 采用渐进式披露架构——Claude 按需分阶段加载信息：

```
┌─────────────────────────────────────────────────────────────┐
│  Level 1: Metadata (始终加载)                               │
│  → YAML Frontmatter (~100 tokens)                           │
│  → name + description                                       │
├─────────────────────────────────────────────────────────────┤
│  Level 2: Instructions (触发时加载)                         │
│  → SKILL.md 正文 (< 5k tokens)                              │
│  → 工作流和指导                                              │
├─────────────────────────────────────────────────────────────┤
│  Level 3+: Resources (按需加载)                             │
│  → 打包文件 (无限制)                                         │
│  → 脚本、模板、文档                                          │
└─────────────────────────────────────────────────────────────┘
```

**关键好处：** 你可以安装很多 Skills 而不会有上下文惩罚。

**Skills 类型与位置：**

| 类型 | 位置 | 范围 | 共享 |
|------|------|------|------|
| **Enterprise** | Managed settings | 所有组织用户 | 是 |
| **Personal** | `~/.claude/skills/<name>/SKILL.md` | 个人 | 否 |
| **Project** | `.claude/skills/<name>/SKILL.md` | 团队 | 是 |
| **Plugin** | `<plugin>/skills/<name>/SKILL.md` | 启用插件处 | 取决于配置 |

优先级：Enterprise > Personal > Project

### 1.3.3 SKILL.md 格式与 Frontmatter 字段详解

**基本目录结构：**

```
my-skill/
├── SKILL.md           # 主指令文件（必需）
├── templates/         # 模板文件
├── examples/          # 示例输出
├── references/        # 参考文档
└── scripts/           # 可执行脚本
```

**SKILL.md 格式：**

```yaml
---
name: your-skill-name
description: Brief description of what this Skill does and when to use it
argument-hint: "[filename] [format]"
allowed-tools: Read, Grep, Glob
disable-model-invocation: true
context: fork
agent: Explore
---

# Your Skill Name

## Instructions
Provide clear, step-by-step guidance for Claude.
```

**Frontmatter 字段详解：**

| 字段 | 说明 |
|------|------|
| `name` | 小写字母、数字、连字符（最多 64 字符） |
| `description` | **关键！** 描述做什么 + 何时使用（最多 1024 字符） |
| `argument-hint` | 参数提示，显示在 `/` 自动补全菜单 |
| `disable-model-invocation` | `true` = 只有用户可以调用，Claude 不会自动触发 |
| `user-invocable` | `false` = 从 `/` 菜单隐藏，只有 Claude 可调用 |
| `allowed-tools` | 预授权的工具列表，无需权限确认 |
| `context: fork` | 在隔离的子代理上下文中运行 |
| `agent` | 子代理类型：`Explore`、`Plan`、`general-purpose` |
| `paths` | Glob 模式，限制技能自动激活的文件范围 |

### 1.3.4 调用控制与字符串替换

**调用控制：**

| Frontmatter | 用户可调用 | Claude 可调用 |
|-------------|-----------|---------------|
| (默认) | ✅ | ✅ |
| `disable-model-invocation: true` | ✅ | ❌ |
| `user-invocable: false` | ❌ | ✅ |

使用场景：
- `disable-model-invocation: true` → 有副作用的操作：`/deploy`、`/commit`
- `user-invocable: false` → 背景知识：`legacy-system-context`

**字符串替换：**

| 变量 | 说明 |
|------|------|
| `$ARGUMENTS` | 所有传入参数 |
| `$ARGUMENTS[N]` 或 `$N` | 按索引访问特定参数（0 起始） |
| `${CLAUDE_SESSION_ID}` | 当前会话 ID |
| `${CLAUDE_SKILL_DIR}` | Skill 目录路径 |
| `` !`command` `` | 执行 shell 命令并内联输出 |

### 1.3.5 在子代理中运行 Skill 与内置 Skills

在子代理中运行 Skill：

```yaml
---
name: deep-research
description: Research a topic thoroughly
context: fork
agent: Explore
---

Research $ARGUMENTS thoroughly:
1. Find relevant files using Glob and Grep
2. Read and analyze the code
3. Summarize findings with specific file references
```

代理类型选择：

| 代理类型 | 最适合 |
|----------|--------|
| `Explore` | 只读研究、代码库分析 |
| `Plan` | 创建实施计划 |
| `general-purpose` | 需要所有工具的广泛任务 |

**内置 Skills：**

| Skill | 说明 |
|-------|------|
| `/simplify` | 审查变更文件的复用性、质量和效率 |
| `/batch <instruction>` | 使用 git worktrees 编排大规模并行变更 |
| `/debug [description]` | 通过读取调试日志排查当前会话 |
| `/loop [interval] <prompt>` | 按间隔重复运行提示 |
| `/claude-api` | 加载 Claude API/SDK 参考 |

---

## 1.4. Subagents（子代理）

### 1.4.1 什么是 Subagents 与核心优势

Subagents 是专门化的 AI 助手，Claude Code 可以将任务委托给它们。每个子代理有：
- 特定的目的
- **独立的上下文窗口**（与主对话分离）
- 可配置的工具集
- 自定义系统提示

| 优势 | 说明 |
|------|------|
| **上下文保留** | 在独立上下文中运行，防止污染主对话 |
| **专业化知识** | 针对特定领域微调，成功率更高 |
| **可复用性** | 跨项目使用，与团队共享 |
| **灵活权限** | 不同子代理类型有不同的工具访问级别 |
| **可扩展性** | 多个代理同时处理不同方面 |

### 1.4.2 配置格式与字段详解

**文件位置：**

| 优先级 | 类型 | 位置 | 范围 |
|--------|------|------|------|
| 1 (最高) | **CLI 定义** | `--agents` 标志 (JSON) | 仅当前会话 |
| 2 | **项目级** | `.claude/agents/` | 当前项目 |
| 3 | **用户级** | `~/.claude/agents/` | 所有项目 |
| 4 (最低) | **插件级** | 插件 `agents/` 目录 | 通过插件 |

**配置格式：**

```yaml
---
name: your-sub-agent-name
description: Description of when this subagent should be invoked
tools: tool1, tool2, tool3
disallowedTools: tool4
model: sonnet
permissionMode: default
maxTurns: 20
skills: skill1, skill2
mcpServers: server1
memory: user
background: false
effort: high
isolation: worktree
---

Your subagent's system prompt goes here.
```

**配置字段详解：**

| 字段 | 必需 | 说明 |
|------|------|------|
| `name` | ✅ | 唯一标识符（小写字母和连字符） |
| `description` | ✅ | 自然语言描述，包含 "use PROACTIVELY" 鼓励自动调用 |
| `tools` | ❌ | 工具列表，省略则继承所有工具 |
| `disallowedTools` | ❌ | 禁止使用的工具 |
| `model` | ❌ | `sonnet`、`opus`、`haiku` 或 `inherit` |
| `permissionMode` | ❌ | 权限模式：`default`、`acceptEdits`、`dontAsk` |
| `maxTurns` | ❌ | 最大代理轮次限制 |
| `skills` | ❌ | 预加载的技能列表 |
| `memory` | ❌ | 持久内存范围：`user`、`project`、`local` |
| `background` | ❌ | 设为 `true` 始终作为后台任务运行 |
| `isolation` | ❌ | 设为 `worktree` 给子代理独立的 git worktree |

### 1.4.3 内置子代理与使用方式

**内置子代理：**

| 代理 | 模型 | 用途 |
|------|------|------|
| **general-purpose** | 继承 | 复杂多步任务 |
| **Plan** | 继承 | 规划模式的研究 |
| **Explore** | Haiku | 只读代码库探索（快速/中等/非常彻底） |
| **Bash** | 继承 | 在独立上下文中执行终端命令 |
| **statusline-setup** | Sonnet | 配置状态栏 |
| **Claude Code Guide** | Haiku | 回答 Claude Code 功能问题 |

**使用方式：**

```
# 自动委托 — description 中包含 "use PROACTIVELY"

# 显式调用
> Use the test-runner subagent to fix failing tests

# @-提及调用
> @"code-reviewer (agent)" review the auth module

# 会话级代理
claude --agent code-reviewer
```

### 1.4.4 可恢复代理、持久内存与 Worktree 隔离

**可恢复代理：**

```bash
# 初始调用
> Use the code-analyzer agent to review the auth module
# Returns agentId: "abc123"

# 稍后恢复
> Resume agent abc123 and now analyze the authorization logic
```

**子代理持久内存：**

```yaml
---
name: researcher
memory: user
---

You are a research assistant. Check your MEMORY.md file at the
start of each session to recall previous context.
```

| 范围 | 目录 | 用途 |
|------|------|------|
| `user` | `~/.claude/agent-memory/<name>/` | 个人笔记和偏好（所有项目） |
| `project` | `.claude/agent-memory/<name>/` | 项目特定知识（团队共享） |
| `local` | `.claude/agent-memory-local/<name>/` | 本地项目知识（不提交） |

**Worktree 隔离：**

```yaml
---
name: feature-builder
isolation: worktree
tools: Read, Write, Edit, Bash, Grep, Glob
---
```

工作原理：子代理在自己的 git worktree 中操作；如果没有更改，worktree 自动清理；如果有更改，返回 worktree 路径和分支名。

**后台子代理快捷键：**

| 快捷键 | 操作 |
|--------|------|
| `Ctrl+B` | 将当前任务转入后台 |
| `Ctrl+F` | 终止所有后台代理 |

### 1.4.5 示例子代理配置

**Secure Reviewer — 只读权限：**

```yaml
---
name: secure-reviewer
description: Security-focused code review specialist with minimal permissions.
tools: Read, Grep
model: inherit
---

# Secure Code Reviewer

This agent has minimal permissions by design:
- Can read files to analyze
- Can search for patterns
- Cannot execute code
- Cannot modify files
```

**Implementation Agent — 完整权限：**

```yaml
---
name: implementation-agent
description: Full-stack implementation specialist for feature development.
tools: Read, Write, Edit, Bash, Grep, Glob
model: inherit
---

# Implementation Agent

This agent has full capabilities:
- Read specifications and existing code
- Write new code files
- Edit existing files
- Run build commands
```

**Code Reviewer — 代码审查：**

```yaml
---
name: code-reviewer
description: Expert code review specialist. Use PROACTIVELY after writing code.
tools: Read, Grep, Glob, Bash
model: inherit
---

# Code Reviewer Agent

When invoked:
1. Run git diff to see recent changes
2. Focus on modified files
3. Begin review immediately

## Review Priorities (in order)
1. **Security Issues**
2. **Performance Problems**
3. **Code Quality**
4. **Test Coverage**
5. **Design Patterns**
```

**Debugger — 调试专家：**

```yaml
---
name: debugger
description: Debugging specialist for errors, test failures. Use PROACTIVELY.
tools: Read, Edit, Bash, Grep, Glob
model: inherit
---

# Debugger Agent

When invoked:
1. Capture error message and stack trace
2. Identify reproduction steps
3. Isolate the failure location
4. Implement minimal fix
5. Verify solution works
```

---

## 1.5. Checkpoints（检查点）

### 1.5.1 什么是 Checkpoints 与核心概念

Checkpoints 允许你保存会话状态并回滚到之前的点。这对于探索不同方法、从错误中恢复、比较替代方案非常有价值。

| 概念 | 说明 |
|------|------|
| **Checkpoint** | 会话状态快照，包括消息、文件修改、工具使用历史 |
| **Rewind** | 返回到之前的检查点，丢弃后续变更 |
| **Branch Point** | 从一个检查点探索多种方法的分支点 |

Claude Code **自动创建检查点**：每次用户输入都创建新检查点，跨会话保留，30 天后自动清理。

### 1.5.2 访问方式与回滚选项

**访问方式：**

- 键盘快捷键：按 `Esc` 两次 (`Esc + Esc`)
- 斜杠命令：`/rewind` 或 `/checkpoint`

**回滚时的 5 个选项：**

| 选项 | 说明 |
|------|------|
| **1. Restore code and conversation** | 恢复文件和消息到检查点状态 |
| **2. Restore conversation** | 仅回滚消息，保留当前代码 |
| **3. Restore code** | 仅恢复文件变更，保留完整对话历史 |
| **4. Summarize from here** | 压缩该点之后的对话为摘要，释放上下文空间 |
| **5. Never mind** | 取消，返回当前状态 |

### 1.5.3 使用场景与实际示例

| 场景 | 工作流 |
|------|--------|
| **探索方法** | 保存 → 尝试 A → 保存 → 回滚 → 尝试 B → 比较 |
| **安全重构** | 保存 → 重构 → 测试 → 失败则回滚 |
| **A/B 测试** | 保存 → 设计 A → 保存 → 回滚 → 设计 B → 比较 |
| **错误恢复** | 发现问题 → 回滚到最后良好状态 |

**示例 — 探索不同方法：**

```
用户: 给 API 添加缓存层

Claude: 我将添加 Redis 缓存...
[在检查点 A 做出变更]

用户: 其实，让我们试试内存缓存

[用户按 Esc+Esc 回滚到检查点 A]
[在检查点 B 实现内存缓存]
```

**分支策略：**

```
1. 初始实现 → 检查点 A
2. 尝试方法 1 → 检查点 B
3. 回滚到检查点 A
4. 尝试方法 2 → 检查点 C
5. 比较 B 和 C 的结果
6. 选择最佳方法继续
```

### 1.5.4 与 Git 的对比与限制

| 特性 | Git | Checkpoints |
|------|-----|-------------|
| **范围** | 文件系统 | 对话 + 文件 |
| **持久性** | 永久 | 基于会话（30天） |
| **粒度** | 提交 | 任意点 |
| **速度** | 较慢 | 即时 |
| **共享** | 是 | 有限 |

**配合使用：** 用 Checkpoints 快速实验，用 Git 提交最终变更。

**限制：**

| 限制 | 说明 |
|------|------|
| **Bash 命令不跟踪** | `rm`、`mv`、`cp` 等文件系统操作不被捕获 |
| **外部变更不跟踪** | 在编辑器、终端等外部做的变更不被捕获 |
| **非版本控制替代** | 使用 Git 进行永久、可审计的变更 |

---

## 1.6. CLI（命令行基础）

### 1.6.1 CLI 架构与核心命令

```
┌─────────────────────────────────────────────────────────────┐
│                    用户终端                                  │
│                       ↓                                     │
│            "claude [options] [query]"                       │
│                       ↓                                     │
│              ┌─────────────────┐                            │
│              │ Claude Code CLI │                            │
│              └────────┬────────┘                            │
│                       │                                     │
│         ┌─────────────┼─────────────┐                      │
│         ↓             ↓             ↓                      │
│    交互式 REPL    打印模式      会话恢复                     │
│    (默认)        (-p)         (-r/-c)                      │
│         ↓             ↓             ↓                      │
│              ┌─────────────────┐                            │
│              │   Claude API    │                            │
│              └─────────────────┘                            │
└─────────────────────────────────────────────────────────────┘
```

**核心 CLI 命令：**

| 命令 | 说明 | 示例 |
|------|------|------|
| `claude` | 启动交互式 REPL | `claude` |
| `claude "query"` | 带初始提示启动 | `claude "explain this project"` |
| `claude -p "query"` | 打印模式 - 查询后退出 | `claude -p "explain this function"` |
| `cat file \| claude -p "query"` | 处理管道内容 | `cat logs.txt \| claude -p "explain"` |
| `claude -c` | 继续最近对话 | `claude -c` |
| `claude -r "<session>"` | 恢复指定会话 | `claude -r "auth-refactor"` |
| `claude update` | 更新到最新版本 | `claude update` |

**核心标志：**

| 标志 | 说明 | 示例 |
|------|------|------|
| `-p, --print` | 打印模式，无交互 | `claude -p "query"` |
| `-c, --continue` | 加载最近对话 | `claude -c` |
| `-r, --resume` | 恢复指定会话 | `claude -r "session-name"` |
| `--model` | 设置模型 | `claude --model opus` |
| `-n, --name` | 会话显示名称 | `claude -n "auth-refactor"` |
| `--effort` | 设置思考努力级别 | `claude --effort high` |
| `--bare` | 最小模式 | `claude --bare` |

### 1.6.2 交互模式 vs 打印模式

**交互模式**（默认）：

```bash
# 启动交互会话
claude

# 带初始提示启动
claude "explain the authentication flow"
```

**打印模式**（非交互）：

```bash
# 单次查询后退出
claude -p "what does this function do?"

# 处理文件内容
cat error.log | claude -p "explain this error"

# 与其他工具链式处理
claude -p "list todos" | grep "URGENT"
```

### 1.6.3 模型选择与输出格式

**模型选择：**

| 模型 | ID | 说明 |
|------|-----|------|
| **Opus 4.6** | `claude-opus-4-6` | 最强大，自适应努力级别 |
| **Sonnet 4.6** | `claude-sonnet-4-6` | 平衡速度和能力 |
| **Haiku 4.5** | `claude-haiku-4-5` | 最快，适合快速任务 |

```bash
# 使用短名称
claude --model opus "complex architectural review"
claude --model sonnet "implement this feature"
claude --model haiku -p "format this JSON"

# 使用 opusplan 别名 (Opus 规划，Sonnet 执行)
claude --model opusplan "design and implement the API"
```

**输出格式：**

| 标志 | 选项 | 说明 |
|------|------|------|
| `--output-format` | `text`, `json`, `stream-json` | 输出格式（打印模式） |
| `--json-schema` | Schema 对象 | 验证 JSON 输出 |
| `--max-budget-usd` | 金额 | 最大花费（打印模式） |

```bash
# JSON 输出用于程序化使用
claude -p --output-format json "list all functions"

# 流式 JSON 用于实时处理
claude -p --output-format stream-json "generate a long report"

# 结构化输出带 Schema 验证
claude -p --json-schema '{"type":"object"}' "find bugs and return as JSON"
```

### 1.6.4 权限管理

| 标志 | 说明 | 示例 |
|------|------|------|
| `--permission-mode` | 权限模式 | `claude --permission-mode plan` |
| `--tools` | 限制可用工具 | `claude --tools "Read,Grep,Glob"` |
| `--allowedTools` | 无需确认的工具 | `claude --allowedTools "Bash(git *)"` |
| `--disallowedTools` | 禁用的工具 | `claude --disallowedTools "Bash(rm:*)"` |

```bash
# 只读模式用于代码审查
claude --permission-mode plan "review this codebase"

# 限制为安全工具
claude --tools "Read,Grep,Glob" -p "find all TODO comments"

# 阻止危险操作
claude --disallowedTools "Bash(rm -rf:*)" "Bash(git push --force:*)"
```

### 1.6.5 高价值使用场景

**1. CI/CD 集成：**

```yaml
# GitHub Actions 示例
name: AI Code Review
on: [pull_request]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Run Code Review
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          claude -p --output-format json \
            --max-turns 1 \
            "Review the changes in this PR" > review.json
```

**2. 脚本管道：**

```bash
# 分析错误日志
tail -1000 /var/log/app/error.log | claude -p "summarize errors and suggest fixes"

# 分析 git 历史
git log --oneline -50 | claude -p "summarize recent development activity"

# 生成文档
cat src/api/*.ts | claude -p "generate API documentation in markdown"
```

**3. 批量处理：**

```bash
# 处理多个文件
for file in src/*.ts; do
  claude -p --model haiku "summarize: $(cat $file)" >> summaries.md
done
```

**关键环境变量：**

| 变量 | 说明 |
|------|------|
| `ANTHROPIC_API_KEY` | API 密钥 |
| `ANTHROPIC_MODEL` | 覆盖默认模型 |
| `CLAUDE_CODE_EFFORT_LEVEL` | 努力级别 |
| `CLAUDE_CODE_DISABLE_AUTO_MEMORY` | 禁用自动记忆 |
| `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS` | 启用实验性代理团队 |

---

# 2. 进阶者篇

---

## 2.1. MCP（模型上下文协议）

### 2.1.1 什么是 MCP 与架构

MCP (Model Context Protocol) 是一种标准化方式，让 Claude 能够访问外部工具、API 和实时数据源。与 Memory 不同，MCP 提供**实时访问动态数据**的能力。

**关键特性：** 实时访问外部服务、实时数据同步、可扩展架构、安全认证、基于工具的交互

**MCP 架构：**

```
Claude
  │
  ├── Filesystem MCP → 本地文件
  ├── GitHub MCP → GitHub 仓库
  ├── Database MCP → PostgreSQL/MySQL
  ├── Slack MCP → Slack 工作区
  └── Google Docs MCP → Google Drive
```

### 2.1.2 安装方法（HTTP / Stdio / OAuth）

**HTTP 传输（推荐）：**

```bash
# 基本 HTTP 连接
claude mcp add --transport http notion https://mcp.notion.com/mcp

# 带认证头的 HTTP
claude mcp add --transport http secure-api https://api.example.com/mcp \
  --header "Authorization: Bearer your-token"
```

**Stdio 传输（本地）：**

```bash
# 本地 Node.js 服务器
claude mcp add --transport stdio myserver -- npx @myorg/mcp-server

# 带环境变量
claude mcp add --transport stdio myserver --env KEY=value -- npx server
```

**OAuth 2.0 认证：**

```bash
# 交互式 OAuth 流程
claude mcp add --transport http my-service https://my-service.example.com/mcp

# 预配置 OAuth 凭据
claude mcp add --transport http my-service https://my-service.example.com/mcp \
  --client-id "your-client-id" \
  --client-secret "your-client-secret" \
  --callback-port 8080
```

### 2.1.3 配置管理与作用域

**MCP 作用域：**

| 作用域 | 位置 | 描述 | 共享 |
|--------|------|------|------|
| **Local** | `~/.claude.json` | 当前用户当前项目私有 | 仅自己 |
| **Project** | `.mcp.json` | 提交到 git 仓库 | 团队成员 |
| **User** | `~/.claude.json` | 所有项目可用 | 仅自己 |

**项目级 MCP 配置 — `.mcp.json`：**

```json
{
  "mcpServers": {
    "github": {
      "type": "http",
      "url": "https://api.github.com/mcp"
    }
  }
}
```

**MCP 配置管理命令：**

```bash
# 添加 HTTP 服务器
claude mcp add --transport http github https://api.github.com/mcp

# 添加本地 stdio 服务器
claude mcp add --transport stdio database -- npx @company/db-server

# 列出所有 MCP 服务器
claude mcp list

# 获取特定服务器详情
claude mcp get github

# 移除 MCP 服务器
claude mcp remove github
```

### 2.1.4 常用 MCP 服务器示例

**GitHub MCP：**

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  }
}
```

可用工具：`list_prs`、`get_pr`、`create_pr`、`list_issues`、`create_issue`、`search_code`

使用示例：`/mcp__github__get_pr 456`

**Database MCP：**

```json
{
  "mcpServers": {
    "database": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-database"],
      "env": {
        "DATABASE_URL": "postgresql://user:pass@localhost/mydb"
      }
    }
  }
}
```

**Filesystem MCP：**

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-filesystem", "/home/user/projects"]
    }
  }
}
```

**环境变量管理** — 在 `~/.bashrc` 或 `~/.zshrc` 中设置敏感凭据：

```bash
export GITHUB_TOKEN="ghp_xxxxxxxxxxxxx"
export DATABASE_URL="postgresql://user:pass@localhost/mydb"
```

### 2.1.5 MCP vs Memory 决策矩阵与输出限制

**决策矩阵：**

```
需要外部数据？
    │
    ├── 否 → 使用 Memory
    │
    └── 是 → 数据是否频繁变化？
              │
              ├── 否/很少 → 使用 Memory
              │
              └── 是/经常 → 使用 MCP
```

| 特性 | Memory | MCP |
|------|--------|-----|
| **数据类型** | 静态、持久 | 动态、实时 |
| **典型用途** | 偏好、上下文、历史 | API、数据库、服务 |
| **更新频率** | 手动更新 | 实时同步 |
| **缓存** | 自动缓存 | 无缓存 |

**MCP 输出限制：**

| 限制 | 阈值 | 行为 |
|------|------|------|
| **警告** | 10,000 tokens | 显示警告，输出较大 |
| **默认最大** | 25,000 tokens | 超出此限制截断输出 |
| **磁盘持久化** | 50,000 字符 | 超出 50K 字符保存到磁盘 |

```bash
# 增加最大输出限制
export MAX_MCP_OUTPUT_TOKENS=50000
```

**MCP Prompts 作为斜杠命令：** `/mcp__<server>__<prompt>`

**MCP 资源 @ 提及：** `@server-name:protocol://resource/path`

**Claude 作为 MCP 服务器：**

```bash
# 启动 Claude Code 作为 MCP 服务器
claude mcp serve

# 在另一个 Claude Code 实例中添加
claude mcp add --transport stdio claude-agent -- claude mcp serve
```

---

## 2.2. Hooks（钩子）

### 2.2.1 什么是 Hooks 与配置位置

Hooks 是在 Claude Code 会话期间响应特定事件而自动执行的脚本。它们实现自动化、验证、权限管理和自定义工作流。

**配置位置：**

| 位置 | 范围 | 共享 |
|------|------|------|
| `~/.claude/settings.json` | 用户设置（所有项目） | 个人 |
| `.claude/settings.json` | 项目设置（可提交） | 团队 |
| `.claude/settings.local.json` | 本地项目设置 | 个人 |
| Managed policy | 组织级设置 | 组织 |
| Plugin `hooks/hooks.json` | 插件范围钩子 | 插件用户 |
| Skill/Agent frontmatter | 组件生命周期钩子 | 组件用户 |

**基本配置结构：**

```json
{
  "hooks": {
    "EventName": [
      {
        "matcher": "ToolPattern",
        "hooks": [
          {
            "type": "command",
            "command": "your-command-here",
            "timeout": 60
          }
        ]
      }
    ]
  }
}
```

**关键字段：**

| 字段 | 描述 | 示例 |
|------|------|------|
| `matcher` | 匹配工具名称的模式（区分大小写） | `"Write"`, `"Edit\|Write"`, `"*"` |
| `hooks` | 钩子定义数组 | `[{ "type": "command", ... }]` |
| `type` | 钩子类型 | `"command"`, `"prompt"`, `"http"`, `"agent"` |
| `command` | 要执行的 shell 命令 | `"$CLAUDE_PROJECT_DIR/.claude/hooks/format.sh"` |
| `timeout` | 可选超时时间（秒，默认 60） | `30` |
| `once` | 如果为 `true`，每次会话只运行一次 | `true` |

### 2.2.2 钩子类型与事件

**钩子类型：**

- **Command Hooks**（默认）：执行 shell 命令，通过 JSON stdin/stdout 和退出码通信
- **HTTP Hooks**：远程 webhook 端点
- **Prompt Hooks**：LLM 评估的提示，主要用于 `Stop` 和 `SubagentStop` 事件
- **Agent Hooks**：基于子代理的验证钩子，可以执行多步推理

**钩子事件：**

| 事件 | 触发时机 | 可阻塞 | 常见用途 |
|------|----------|--------|----------|
| **SessionStart** | 会话开始/恢复/清除/压缩 | 否 | 环境设置 |
| **UserPromptSubmit** | 用户提交提示 | 是 | 验证提示 |
| **PreToolUse** | 工具执行前 | 是 | 验证、修改输入 |
| **PermissionRequest** | 权限对话框显示 | 是 | 自动批准/拒绝 |
| **PostToolUse** | 工具成功后 | 否 | 添加上下文、反馈 |
| **PostToolUseFailure** | 工具执行失败 | 否 | 错误处理、日志 |
| **SubagentStart** | 子代理启动 | 否 | 子代理设置 |
| **SubagentStop** | 子代理完成 | 是 | 子代理验证 |
| **Stop** | Claude 完成响应 | 是 | 任务完成检查 |
| **SessionEnd** | 会话终止 | 否 | 清理、最终日志 |

**匹配器模式：**

| 模式 | 描述 | 示例 |
|------|------|------|
| 精确字符串 | 匹配特定工具 | `"Write"` |
| 正则模式 | 匹配多个工具 | `"Edit\|Write"` |
| 通配符 | 匹配所有工具 | `"*"` 或 `""` |
| MCP 工具 | 服务器和工具模式 | `"mcp__memory__.*"` |

### 2.2.3 PreToolUse 与 PostToolUse 钩子

**PreToolUse** — 在 Claude 创建工具参数后、处理前运行：

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/validate-bash.py"
          }
        ]
      }
    ]
  }
}
```

PreToolUse 输出控制：
- `permissionDecision`: `"allow"`, `"deny"`, 或 `"ask"`
- `permissionDecisionReason`: 决定原因
- `updatedInput`: 修改后的工具输入参数

**PostToolUse** — 工具完成后立即运行：

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/security-scan.py"
          }
        ]
      }
    ]
  }
}
```

PostToolUse 输出控制：
- `"block"` 决定向 Claude 提供反馈
- `additionalContext`: 为 Claude 添加的上下文

### 2.2.4 JSON 输入输出与退出码

**JSON 输入（通过 stdin）：**

```json
{
  "session_id": "abc123",
  "transcript_path": "/path/to/transcript.jsonl",
  "cwd": "/current/working/directory",
  "hook_event_name": "PreToolUse",
  "tool_name": "Write",
  "tool_input": {
    "file_path": "/path/to/file.js",
    "content": "..."
  }
}
```

**退出码：**

| 退出码 | 含义 | 行为 |
|--------|------|------|
| **0** | 成功 | 继续，解析 JSON stdout |
| **2** | 阻塞错误 | 阻塞操作，stderr 显示为错误 |
| **其他** | 非阻塞错误 | 继续，stderr 在详细模式下显示 |

**JSON 输出（stdout，退出码 0）：**

```json
{
  "continue": true,
  "stopReason": "Optional message if stopping",
  "systemMessage": "Optional warning message",
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "permissionDecision": "allow",
    "permissionDecisionReason": "File is in allowed directory",
    "updatedInput": {
      "file_path": "/modified/path.js"
    }
  }
}
```

**环境变量：**

| 变量 | 可用性 | 描述 |
|------|--------|------|
| `CLAUDE_PROJECT_DIR` | 所有钩子 | 项目根目录的绝对路径 |
| `CLAUDE_ENV_FILE` | SessionStart, CwdChanged, FileChanged | 持久化环境变量的文件路径 |
| `CLAUDE_CODE_REMOTE` | 所有钩子 | 远程环境运行时为 `"true"` |

### 2.2.5 示例钩子脚本

**示例 1 — Bash 命令验证器（PreToolUse）：**

`.claude/hooks/validate-bash.py`

```python
#!/usr/bin/env python3
import json
import sys
import re

BLOCKED_PATTERNS = [
    (r"\brm\s+-rf\s+/", "Blocking dangerous rm -rf / command"),
    (r"\bsudo\s+rm", "Blocking sudo rm command"),
]

def main():
    input_data = json.load(sys.stdin)
    tool_name = input_data.get("tool_name", "")
    if tool_name != "Bash":
        sys.exit(0)

    command = input_data.get("tool_input", {}).get("command", "")

    for pattern, message in BLOCKED_PATTERNS:
        if re.search(pattern, command):
            print(message, file=sys.stderr)
            sys.exit(2)  # Exit 2 = blocking error

    sys.exit(0)

if __name__ == "__main__":
    main()
```

**示例 2 — 安全扫描器（PostToolUse）：**

`.claude/hooks/security-scan.py`

```python
#!/usr/bin/env python3
import json
import sys
import re

SECRET_PATTERNS = [
    (r"password\s*=\s*['\"][^'\"]+['\"]", "Potential hardcoded password"),
    (r"api[_-]?key\s*=\s*['\"][^'\"]+['\"]", "Potential hardcoded API key"),
]

def main():
    input_data = json.load(sys.stdin)
    tool_name = input_data.get("tool_name", "")
    if tool_name not in ["Write", "Edit"]:
        sys.exit(0)

    tool_input = input_data.get("tool_input", {})
    content = tool_input.get("content", "") or tool_input.get("new_string", "")

    warnings = []
    for pattern, message in SECRET_PATTERNS:
        if re.search(pattern, content, re.IGNORECASE):
            warnings.append(message)

    if warnings:
        output = {
            "hookSpecificOutput": {
                "hookEventName": "PostToolUse",
                "additionalContext": f"Security warnings: " + "; ".join(warnings)
            }
        }
        print(json.dumps(output))

    sys.exit(0)

if __name__ == "__main__":
    main()
```

**示例 3 — 自动格式化代码（PostToolUse）：**

`.claude/hooks/format-code.sh`

```bash
#!/bin/bash

INPUT=$(cat)
TOOL_NAME=$(echo "$INPUT" | python3 -c "import sys, json; print(json.load(sys.stdin).get('tool_name', ''))")
FILE_PATH=$(echo "$INPUT" | python3 -c "import sys, json; print(json.load(sys.stdin).get('tool_input', {}).get('file_path', ''))")

if [ "$TOOL_NAME" != "Write" ] && [ "$TOOL_NAME" != "Edit" ]; then
    exit 0
fi

# 根据文件扩展名格式化
case "$FILE_PATH" in
    *.js|*.jsx|*.ts|*.tsx|*.json)
        command -v prettier &>/dev/null && prettier --write "$FILE_PATH" 2>/dev/null
        ;;
    *.py)
        command -v black &>/dev/null && black "$FILE_PATH" 2>/dev/null
        ;;
    *.go)
        command -v gofmt &>/dev/null && gofmt -w "$FILE_PATH" 2>/dev/null
        ;;
esac

exit 0
```

**示例 4 — 提示验证器（UserPromptSubmit）：**

`.claude/hooks/validate-prompt.py`

```python
#!/usr/bin/env python3
import json
import sys
import re

BLOCKED_PATTERNS = [
    (r"delete\s+(all\s+)?database", "Dangerous: database deletion"),
    (r"rm\s+-rf\s+/", "Dangerous: root deletion"),
]

def main():
    input_data = json.load(sys.stdin)
    prompt = input_data.get("user_prompt", "") or input_data.get("prompt", "")

    for pattern, message in BLOCKED_PATTERNS:
        if re.search(pattern, prompt, re.IGNORECASE):
            output = {
                "decision": "block",
                "reason": f"Blocked: {message}"
            }
            print(json.dumps(output))
            sys.exit(0)

    sys.exit(0)

if __name__ == "__main__":
    main()
```

**示例 5 — 智能停止钩子（Prompt-Based）：**

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "prompt",
            "prompt": "Review if Claude completed all requested tasks. Check: 1) Were all files created/modified? 2) Were there unresolved errors?",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

### 2.2.6 安全注意事项与调试

**安全注意事项：**

| ✅ 应该做 | ❌ 不应该做 |
|----------|------------|
| 验证和清理所有输入 | 盲目信任输入数据 |
| 引用 shell 变量：`"$VAR"` | 使用未引用：`$VAR` |
| 阻塞路径遍历（`..`） | 允许任意路径 |
| 使用 `$CLAUDE_PROJECT_DIR` 绝对路径 | 硬编码路径 |
| 跳过敏感文件（`.env`, `.git/`, 密钥） | 处理所有文件 |

**调试：**

```bash
# 启用调试模式
claude --debug

# 在 Claude Code 中使用 Ctrl+O 启用详细模式

# 独立测试钩子
echo '{"tool_name": "Bash", "tool_input": {"command": "ls -la"}}' | python3 .claude/hooks/validate-bash.py

# 检查退出码
echo $?
```

**钩子执行细节：**

| 方面 | 行为 |
|------|------|
| **超时** | 默认 60 秒，可配置 |
| **并行化** | 所有匹配的钩子并行运行 |
| **去重** | 相同的钩子命令去重 |
| **环境** | 在当前目录运行，使用 Claude Code 环境 |

---

# 3. 高级用户篇

---

## 3.1. Plugins（插件）

### 3.1.1 什么是 Plugins 与核心优势

Plugins 是打包的功能集合，将斜杠命令、子代理、MCP 服务器和钩子组合成一键安装的扩展包。它们是最高级别的扩展机制，用于创建可共享、可分发的完整工作流。

| 优势 | 说明 |
|------|------|
| **一键安装** | 单条命令安装所有组件 |
| **自动配置** | 无需手动设置各个部分 |
| **版本管理** | 自动处理版本和更新 |
| **团队共享** | 通过市场或仓库轻松分发 |
| **依赖管理** | 自动处理依赖关系 |

**插件架构：**

```
┌─────────────────────────────────────────────────────────────┐
│                      Plugin                                  │
├─────────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐         │
│  │ Slash Cmds  │  │ Subagents   │  │ MCP Servers │         │
│  │  commands/  │  │  agents/    │  │  .mcp.json  │         │
│  └─────────────┘  └─────────────┘  └─────────────┘         │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐         │
│  │   Hooks     │  │    LSP      │  │   Skills    │         │
│  │  hooks/     │  │  .lsp.json  │  │  skills/    │         │
│  └─────────────┘  └─────────────┘  └─────────────┘         │
└─────────────────────────────────────────────────────────────┘
```

### 3.1.2 插件目录结构与 plugin.json 清单

**插件目录结构：**

```
my-plugin/
├── .claude-plugin/
│   └── plugin.json       # 清单文件（必需）
├── commands/             # 斜杠命令
│   ├── task-1.md
│   └── workflows/
├── agents/               # 子代理定义
│   └── specialist.md
├── skills/               # 技能定义
│   └── skill-1/
│       └── SKILL.md
├── hooks/                # 事件钩子
│   └── hooks.json
├── .mcp.json             # MCP 服务器配置
├── .lsp.json             # LSP 服务器配置
├── bin/                  # 可执行文件
├── settings.json         # 默认设置
├── templates/            # 模板文件
├── scripts/              # 辅助脚本
└── docs/                 # 文档
```

**plugin.json 清单文件：**

```json
{
  "name": "my-plugin",
  "description": "插件功能描述",
  "version": "1.0.0",
  "author": {
    "name": "Your Name"
  },
  "homepage": "https://example.com",
  "repository": "https://github.com/user/repo",
  "license": "MIT",
  "userConfig": {
    "apiKey": {
      "description": "API 密钥",
      "sensitive": true
    },
    "region": {
      "description": "部署区域",
      "default": "us-east-1"
    }
  }
}
```

### 3.1.3 LSP 服务器配置

插件可以包含语言服务器协议 (LSP) 支持，提供实时代码智能。

```json
{
  "python": {
    "command": "pyright-langserver",
    "args": ["--stdio"],
    "extensionToLanguage": {
      ".py": "python",
      ".pyi": "python"
    }
  },
  "typescript": {
    "command": "typescript-language-server",
    "args": ["--stdio"],
    "extensionToLanguage": {
      ".ts": "typescript",
      ".tsx": "typescriptreact"
    }
  }
}
```

LSP 能力：即时诊断、代码导航、悬停信息、符号列表

### 3.1.4 安装管理与市场

**安装与管理命令：**

```bash
# 从市场安装
/plugin install plugin-name
claude plugin install plugin-name@marketplace-name

# 从 GitHub 安装
/plugin install github:username/repo

# 从本地路径安装（开发测试）
claude --plugin-dir ./my-plugin

# 列出已安装插件
/plugin list --installed

# 启用/禁用插件
/plugin enable plugin-name
/plugin disable plugin-name

# 更新插件
/plugin update plugin-name

# 卸载插件
/plugin uninstall plugin-name
```

**插件来源类型：**

| 来源 | 语法 | 示例 |
|------|------|------|
| 相对路径 | 字符串路径 | `"./plugins/my-plugin"` |
| GitHub | `{ "source": "github", "repo": "owner/repo" }` | `{ "source": "github", "repo": "acme/lint-plugin" }` |
| Git URL | `{ "source": "url", "url": "..." }` | `{ "source": "url", "url": "https://git.internal/plugin.git" }` |
| npm | `{ "source": "npm", "package": "..." }` | `{ "source": "npm", "package": "@acme/claude-plugin" }` |
| pip | `{ "source": "pip", "package": "..." }` | `{ "source": "pip", "package": "claude-data-plugin" }` |

### 3.1.5 企业管理与安全

**企业管理设置：**

| 设置 | 描述 |
|------|------|
| `enabledPlugins` | 默认启用的插件白名单 |
| `deniedPlugins` | 禁止安装的插件黑名单 |
| `extraKnownMarketplaces` | 添加额外的市场来源 |
| `strictKnownMarketplaces` | 限制用户可添加的市场 |

插件子代理在受限沙箱中运行，以下 frontmatter 键不被允许：`hooks`、`mcpServers`、`permissionMode`

插件可通过 `${CLAUDE_PLUGIN_DATA}` 环境变量访问持久化状态目录。

**插件 vs 独立配置：**

| 方面 | 独立配置 | 插件 |
|------|----------|------|
| **安装时间** | 2+ 小时 | 2 分钟 |
| **配置** | 手动设置 | 自动配置 |
| **版本控制** | 手动 | 自动 |
| **团队共享** | 复制文件 | 安装 ID |
| **更新** | 手动 | 自动可用 |

---

## 3.2. Advanced Features（高级功能）

### 3.2.1 规划模式与 Ultraplan

**规划模式** 让 Claude 在实施前先思考复杂任务，创建可审查和批准的详细计划。

**两阶段方法：** 规划阶段（分析任务，创建详细实施计划）→ 实施阶段（批准后执行计划）

激活方式：

```bash
# 斜杠命令
/plan Implement user authentication system

# CLI 标志
claude --permission-mode plan

# 键盘快捷键
Shift + Tab    # 切换权限模式
```

**Ultraplan（v2.1.101 新增）** 将规划任务从本地 CLI 移交给云端 Claude Code 会话。

```bash
/ultraplan migrate the auth service from sessions to JWTs
```

状态指示：

| 状态 | 含义 |
|------|------|
| `◇ ultraplan` | Claude 正在研究代码库并起草计划 |
| `◇ ultraplan needs your input` | Claude 有澄清问题 |
| `◆ ultraplan ready` | 计划可在浏览器中审查 |

### 3.2.2 深度思考与自动模式

**深度思考** 让 Claude 花更多时间推理复杂问题。

激活方式：

| 方法 | 操作 |
|------|------|
| 键盘快捷键 | `Option + T` (macOS) / `Alt + T` (Windows/Linux) |
| 环境变量 | `export MAX_THINKING_TOKENS=16000` |
| CLI 标志 | `claude --effort high` |
| 斜杠命令 | `/effort high` |

努力级别（仅 Opus 4.6）：

| 级别 | 符号 | 说明 |
|------|------|------|
| `low` | ○ | 最少推理 |
| `medium` | ◐ | 中等推理 |
| `high` | ● | 深度推理 |
| `max` | ●● | 最大推理（仅 Opus 4.6） |

**自动模式**（研究预览）使用后台安全分类器审查每个操作，允许 Claude 自主工作同时阻止危险操作。

```bash
claude --enable-auto-mode
claude --permission-mode auto
```

默认阻止的操作：管道安装、发送敏感数据、生产部署、批量删除、IAM 变更、强制推送主分支

默认允许的操作：本地文件操作、声明依赖安装、只读 HTTP、推送当前分支

### 3.2.3 后台任务与监控工具

**后台任务：**

```
用户: Run tests in background

Claude: Started task bg-1234

/task list           # 显示所有任务
/task status bg-1234 # 检查进度
/task show bg-1234   # 查看输出
/task cancel bg-1234 # 取消任务
```

**监控工具**（v2.1.98 新增）让 Claude 监视后台命令的 stdout 并在匹配事件出现时立即反应。轮询每个周期都会消耗完整的 API 往返，而监控在命令静默时零 token 消耗。

常见模式 — 流过滤器：

```bash
tail -f /var/log/app.log | grep --line-buffered "ERROR"
```

> **警告：** 使用 `grep --line-buffered` 避免缓冲延迟

### 3.2.4 定时任务与权限模式

**定时任务：**

```bash
# 显式间隔
/loop 5m check if the deployment finished

# 自然语言
/loop check build status every 30 minutes

# 一次性提醒
remind me at 3pm to push the release branch
```

限制：每会话最多 50 个任务；会话结束时清除；循环任务 3 天后自动过期

**6 种权限模式：**

| 模式 | 行为 |
|------|------|
| `default` | 仅读取文件；其他操作需提示 |
| `acceptEdits` | 自动读取和编辑文件；命令需提示 |
| `plan` | 仅读取文件（研究模式，无编辑） |
| `auto` | 所有操作经后台安全分类器检查 |
| `bypassPermissions` | 所有操作，无权限检查（危险） |
| `dontAsk` | 仅预批准工具执行；其他拒绝 |

激活方式：`Shift + Tab` 循环切换、`/plan`、`claude --permission-mode plan`

### 3.2.5 交互功能（快捷键 / Vim 模式 / 语音输入 / 频道）

**键盘快捷键：**

| 快捷键 | 描述 |
|--------|------|
| `Ctrl+C` | 取消当前输入/生成 |
| `Ctrl+D` | 退出 Claude Code |
| `Ctrl+G` | 在外部编辑器中编辑计划 |
| `Ctrl+L` | 清除终端屏幕 |
| `Ctrl+O` | 切换详细输出（查看推理） |
| `Ctrl+R` | 反向搜索历史 |
| `Ctrl+T` | 切换任务列表视图 |
| `Ctrl+B` | 后台运行任务 |
| `Esc+Esc` | 回滚代码/对话 |
| `Shift+Tab` / `Alt+M` | 切换权限模式 |
| `Option+P` / `Alt+P` | 切换模型 |
| `Option+T` / `Alt+T` | 切换深度思考 |

**自定义键绑定** — 运行 `/keybindings` 打开 `~/.claude/keybindings.json`

**Vim 模式** — 使用 `/vim` 启用：`Esc` 进入 NORMAL 模式，`i/a/o` 进入 INSERT 模式；标准导航 `h/l`, `j/k`, `w/b/e`, `0/$`

**Bash 模式** — 使用 `!` 前缀直接执行 shell 命令：`! npm test`

**语音输入** — `/voice` 启用按键说话，支持 20 种语言

**频道**（研究预览）— 通过 MCP 服务器将外部服务的事件推送到运行中的会话：

```bash
claude --channels discord,telegram
```

### 3.2.6 Chrome 集成与远程控制

**Chrome 集成** 将 Claude Code 连接到 Chrome 或 Edge 浏览器：

```bash
claude --chrome
# 或在会话中
/chrome
```

能力：实时调试、设计验证、表单验证、Web 应用测试、数据提取

**远程控制** 让你从手机、平板或任何浏览器继续本地运行的会话：

```bash
# CLI
claude remote-control
claude remote-control --name "Auth Refactor"

# 会话中
/remote-control
```

连接方式：会话 URL、二维码、按名称查找

**Web 会话：**

```bash
# 从 CLI 创建 Web 会话
claude --remote "implement the new API endpoints"

# 恢复 Web 会话到本地
claude --teleport
```

### 3.2.7 桌面应用与 Git Worktrees

**桌面应用** 提供独立应用，具有可视化差异审查、并行会话和集成连接器。

核心功能：

| 功能 | 描述 |
|------|------|
| **差异视图** | 逐文件可视化审查，内联评论 |
| **应用预览** | 自动启动开发服务器和嵌入式浏览器 |
| **PR 监控** | GitHub CLI 集成，自动修复 CI 失败 |
| **并行会话** | 侧边栏多会话，自动 Git worktree 隔离 |
| **定时任务** | 在应用打开时运行的周期性任务 |

从 CLI 切换：`/desktop`

**Git Worktrees** 让你在隔离的 worktree 中启动 Claude Code：

```bash
claude --worktree
claude -w
```

位置：`<repo>/.claude/worktrees/<name>`

单仓库稀疏检出：

```json
{
  "worktree": {
    "sparsePaths": ["packages/my-package", "shared/"]
  }
}
```

### 3.2.8 沙箱与管理设置

**沙箱** 为 Bash 命令提供操作系统级别的文件系统和网络隔离：

```bash
claude --sandbox
# 或
/sandbox
```

配置：

```json
{
  "sandbox": {
    "enabled": true,
    "failIfUnavailable": true,
    "filesystem": {
      "allowWrite": ["/Users/me/project"],
      "allowRead": ["/Users/me/project", "/usr/local/lib"],
      "denyRead": ["/Users/me/.ssh", "/Users/me/.aws"]
    }
  }
}
```

**管理设置** 让企业管理员使用平台原生管理工具部署配置。

部署方法：

| 平台 | 方法 | 版本 |
|------|------|------|
| macOS | 托管 plist 文件 (MDM) | v2.1.51+ |
| Windows | Windows 注册表 | v2.1.51+ |
| 跨平台 | 托管配置文件 | v2.1.51+ |
| 跨平台 | 托管 drop-ins (`managed-settings.d/`) | v2.1.83+ |

托管 drop-ins 目录：

```
~/.claude/managed-settings.d/
  00-org-defaults.json
  10-team-policies.json
  20-project-overrides.json
```

### 3.2.9 代理团队

**代理团队**（实验性功能）让多个 Claude Code 实例协作完成任务。

```bash
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
```

工作方式：团队领导协调整体任务，委派子任务；队友独立工作，各有自己的上下文窗口；共享任务列表实现队友间的自协调

显示模式：

| 模式 | 描述 |
|------|------|
| `in-process` (默认) | 队友在同一终端进程中运行 |
| `tmux` | 每个队友获得独立的分割窗格 |
| `auto` | 自动选择最佳显示模式 |

```bash
claude --teammate-mode tmux
```

**关键环境变量汇总：**

| 变量 | 描述 |
|------|------|
| `ANTHROPIC_MODEL` | 覆盖默认模型 |
| `MAX_THINKING_TOKENS` | 思考 token 预算 |
| `CLAUDE_CODE_EFFORT_LEVEL` | 努力级别 (low/medium/high/max) |
| `CLAUDE_CODE_DISABLE_AUTO_MEMORY` | 禁用自动记忆 |
| `CLAUDE_CODE_DISABLE_CRON` | 禁用定时任务 |
| `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS` | 启用代理团队 |
| `CLAUDE_CODE_TASK_LIST_ID` | 命名任务目录 |
| `MAX_MCP_OUTPUT_TOKENS` | MCP 输出限制 |

---

# 参考文章

1. [Claude Code 概述 - Claude Code Docs](https://code.claude.com/docs/zh-CN)
2. [Claude How To - GitHub](https://github.com/twardoch/claude-howto)
