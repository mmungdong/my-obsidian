---
title: AI Coding - Claude code 使用笔记
date: 2025-11-11
categories:
  - 代码笔记
tags:
  - AI
  - VibeCoding
archive: false
hide: false
---

> [Claude Code 概述 - Claude Code Docs](https://code.claude.com/docs/zh-CN)

# 1. 安装

## 1.1. 下载

```shell
npm install -g @anthropic-ai/claude-code
```

## 1.2. 配置

> 配置参考：[Claude Code 设置 - Claude Code Docs](https://code.claude.com/docs/zh-CN/settings)

以 wsl 为例，在 ~/.bashrc 中添加如下配置即可：

```shell
# claude code
# 设置权限跳过模式，核心作用是绕过代码执行前的权限校验环节
alias claude='claude --dangerously-skip-permissions'
# 百炼 API EP
export ANTHROPIC_BASE_URL=https://dashscope.aliyuncs.com/apps/anthropic
export ANTHROPIC_AUTH_TOKEN=sk-**************
# model id
export ANTHROPIC_MODEL=qwen3-coder-plus
```

vscode 中的 Claude Code for VS Code 插件配置：

打开下面3个配置：

1. 保证 vscode 可以设置权限跳过模式

![image.png](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20251209231429114.png?imageSlim)

2. 初始化时使用权限跳过模式，Claude Code 在控制台中开启

![image.png](https://images-1306852673.cos.ap-chengdu.myqcloud.com/blog20251209231549393.png?imageSlim)

## 1.3. 使用 zcf 来配置 Claude Code

> 🔗 [GitHub - UfoMiao/zcf: Zero-Config Code Flow for Claude code & Codex](https://github.com/UfoMiao/zcf)
> 🔗 [ZCF - Zero-Config Code Flow | ZCF](https://zcf.ufomiao.com/zh-CN/)

前提：需要本地安装 node 环境

快速启动：
```bash
npx zcf
```

会出现如下提示：

```bash
[mungdong@dev ~]$ npx zcf
Need to install the following packages:
zcf@3.4.2
Ok to proceed? (y) y

✔ Select ZCF display language / 选择ZCF显示语言 1. 简体中文

╔════════════════════════════════════════════════════════════════╗
║                                                                ║
║   ███████╗  ██████╗ ███████╗                                   ║
║       ██╔╝  ██╔═══╝  ██╔═══╝                                   ║
║      ██╔╝   ██║      █████╗                                    ║
║    ██╔╝     ██║      ██╔══╝                                    ║
║   ███████╗  ╚██████╗ ██║                                       ║
║   ╚══════╝   ╚═════╝ ╚═╝        for Claude Code                ║
║                                                                ║
║   Zero-Config Code Flow                                        ║
║                                                                ║
╚════════════════════════════════════════════════════════════════╝

  Version: 3.4.2  |  https://github.com/UfoMiao/zcf

请选择功能
  -------- Claude Code --------
  1. 完整初始化 - 安装 Claude Code + 导入工作流 + 配置 API 或 CCR 代理 + 配置 MCP 服务
  2. 导入工作流 - 仅导入/更新工作流相关文件
  3. 配置 API 或 CCR 代理 - 配置 API URL、认证信息或 CCR 代理
  4. 配置 MCP - 配置 MCP 服务（含 Windows 修复）
  5. 配置默认模型 - 设置默认模型（opus/sonnet/sonnet 1m/自定义）
  6. 配置 Claude 全局记忆 - 配置 AI 输出语言和输出风格
  7. 导入推荐环境变量和权限配置 - 导入隐私保护环境变量和系统权限配置

  --------- 其他工具 ----------
  R. CCR - 配置 Claude Code Router 以使用多个 AI 模型
  U. ccusage - Claude Code 用量分析
  L. CCometixLine - 基于 Rust 的高性能 Claude Code 状态栏工具，集成 Git 信息和实时使用量跟踪

  ------------ ZCF ------------
  8. 更改显示语言 / Select display language - 更改 ZCF 界面语言
  S. 切换代码工具 - 在支持的代码工具之间切换 (Claude Code, Codex)
  -. 卸载和删除配置 - 从系统中删除 Claude Code 配置和工具
  +. 检查更新 - 检查并更新 Claude Code、CCR 和 CCometixLine 的版本
  Q. 退出
```

如果是第一次使用选择 1 进行完整初始化，否则根据自己需要修改配置即可，它会帮助我们初始化好本地所有的 Claude Code 环境，而且会带有一些工作流以及 git worktree 的使用方法，相对来说还是挺不错的。具体可以查看官网，不做赘述。（注意选择 中文 时会耗费更多的 token）

# 2. 命令速查手册

> 整理自官方文档 `code.claude.com/docs`（2025最新版）

## 2.1. 斜杠命令

在交互模式中输入 `/` 触发，按功能分类如下：

### 2.1.1. 会话管理

| 命令 | 说明 |
|------|------|
| `/clear` | 清除当前对话历史，释放上下文。别名：`/reset`、`/new` |
| `/compact [说明]` | 压缩对话内容，减少 token 消耗，可指定关注重点 |
| `/rename [name]` | 重命名当前会话，不加参数则自动生成 |
| `/resume [session]` | 恢复历史会话（按名称/ID），别名：`/continue` |
| `/fork [name]` | 从当前对话节点创建一个分支会话 |
| `/export [filename]` | 导出当前对话为纯文本文件，或复制到剪贴板 |
| `/rewind` | 回退对话和/或代码到之前的某个节点，别名：`/checkpoint` |
| `/exit` | 退出 CLI，别名：`/quit` |

### 2.1.2. 模型与模式

| 命令 | 说明 |
|------|------|
| `/model [model]` | 切换 AI 模型（如 Sonnet/Opus），支持左右键调节 effort level |
| `/fast [on\|off]` | 切换快速模式开关 |
| `/vim` | 切换 Vim / Normal 编辑模式 |
| `/plan` | 直接进入计划模式（Plan Mode） |
| `/sandbox` | 切换沙盒模式（仅支持的平台可用） |
| `/output-style [style]` | 切换输出风格：Default / Explanatory / Learning / 自定义 |

### 2.1.3. 项目与记忆

| 命令 | 说明 |
|------|------|
| `/init` | 初始化项目，创建 `CLAUDE.md` 上下文记忆文件 |
| `/memory` | 编辑 `CLAUDE.md` 记忆文件，管理自动记忆 |
| `/add-dir <path>` | 添加额外工作目录供 Claude 访问 |
| `/context` | 可视化当前上下文使用情况（彩色网格展示） |

### 2.1.4. 代码审查与安全

| 命令 | 说明 |
|------|------|
| `/review` | 对 Pull Request 进行代码审查（质量/正确性/安全/测试覆盖） |
| `/pr-comments [PR]` | 获取 GitHub PR 的评论，自动检测当前分支 PR |
| `/diff` | 打开交互式 diff 查看器，查看未提交的更改和每轮修改 |
| `/security-review` | 分析当前分支待提交的变更中的安全漏洞 |

### 2.1.5. 配置与权限

| 命令 | 说明 |
|------|------|
| `/config` | 打开设置界面，别名：`/settings` |
| `/permissions` | 查看/修改工具权限设置，别名：`/allowed-tools` |
| `/theme` | 更换颜色主题（含暗色/亮色/色盲友好/ANSI 主题） |
| `/terminal-setup` | 配置终端快捷键（Shift+Enter 等） |
| `/keybindings` | 打开或创建快捷键配置文件 |
| `/statusline` | 配置状态栏显示内容 |
| `/privacy-settings` | 查看/更新隐私设置（Pro/Max 用户可用） |

### 2.1.6. 扩展集成

| 命令 | 说明 |
|------|------|
| `/mcp` | 管理 MCP 服务器连接和 OAuth 认证 |
| `/plugin` | 管理 Claude Code 插件 |
| `/skills` | 列出所有可用技能（Skills） |
| `/agents` | 管理子代理（Agent）配置 |
| `/hooks` | 管理工具事件的钩子配置 |
| `/ide` | 管理 IDE 集成并查看状态 |
| `/chrome` | 配置 Claude in Chrome 设置 |
| `/install-github-app` | 安装 Claude GitHub Actions 应用 |
| `/install-slack-app` | 安装 Claude Slack 应用 |

### 2.1.7. 账户与状态

| 命令 | 说明 |
|------|------|
| `/login` | 登录 Anthropic 账户 |
| `/logout` | 登出 Anthropic 账户 |
| `/status` | 查看版本、模型、账户、连接状态 |
| `/cost` | 查看 token 使用统计 |
| `/usage` | 查看计划使用量限制和速率限制状态 |
| `/stats` | 可视化每日使用量、会话历史、连续使用天数、模型偏好 |
| `/upgrade` | 打开升级页面 |
| `/doctor` | 诊断安装与配置是否正常 |

### 2.1.8. 其他实用命令

| 命令 | 说明 |
|------|------|
| `/help` | 查看帮助和所有可用命令 |
| `/feedback [内容]` | 提交反馈/报告 Bug，别名：`/bug` |
| `/copy` | 复制上一条 AI 回复到剪贴板（代码块可交互选择） |
| `/release-notes` | 查看完整更新日志 |
| `/insights` | 生成使用分析报告（项目领域/交互模式/痛点等） |
| `/tasks` | 查看和管理后台任务 |
| `/desktop` | 在 Claude Code 桌面应用中继续当前会话，别名：`/app` |
| `/mobile` | 显示下载移动端 App 的二维码，别名：`/ios`、`/android` |

### 2.1.9. 内置捆绑技能（Bundled Skills）

| 命令 | 说明 |
|------|------|
| `/simplify` | 审查最近修改的文件，检查代码复用、质量、效率问题并自动修复 |
| `/batch <指令>` | 大规模并行代码变更编排，分解为独立单元在 git worktree 中运行 |
| `/debug [描述]` | 通过读取 session 调试日志来排查当前会话问题 |

## 2.2. CLI 命令与参数

### 2.2.1. CLI 命令（终端中使用）

| 命令 | 说明 | 示例 |
|------|------|------|
| `claude` | 启动交互式会话 (REPL) | `claude` |
| `claude "..."` | 带初始提示进入会话 | `claude "explain this project"` |
| `claude -p "..."` | 非交互模式，打印结果后退出 | `claude -p "explain this function"` |
| `cat file \| claude -p "..."` | 管道输入文件处理 | `cat main.go \| claude -p "review this"` |
| `claude -c` | 继续最近一次会话 | `claude -c` |
| `claude -r <session-id>` | 恢复指定会话 | `claude -r abc123 "continue"` |
| `claude update` | 更新到最新版本 | `claude update` |
| `claude mcp` | 配置 MCP 服务器 | `claude mcp` |

### 2.2.2. CLI 参数

| 参数 | 说明 | 示例 |
|------|------|------|
| `--print`, `-p` | 非交互模式运行 | `claude -p "..."` |
| `--continue`, `-c` | 继续最近会话 | `claude -c` |
| `--resume <id>` | 恢复指定会话 | `--resume abc123` |
| `--model` | 指定使用的模型 | `--model claude-sonnet-4-20250514` |
| `--add-dir` | 添加工作目录 | `claude --add-dir ../lib` |
| `--allowedTools` | 允许的工具白名单 | `"Bash(git log:*)"` |
| `--disallowedTools` | 禁用的工具黑名单 | `"Edit"` |
| `--output-format` | 输出格式（text/json/stream-json） | `--output-format json` |
| `--verbose` | 开启详细日志 | `claude --verbose` |
| `--max-turns` | 设置最大对话轮次 | `--max-turns 3` |
| `--dangerously-skip-permissions` | 跳过所有权限提示（⚠️ 危险） | — |

## 2.3. 快捷键

### 2.3.1. 通用控制

| 快捷键 | 说明 |
|--------|------|
| `Ctrl+C` | 取消当前输入 / 中断生成 |
| `Ctrl+D` | 退出会话 |
| `Ctrl+L` | 清屏（保留对话历史） |
| `Ctrl+R` | 反向搜索历史命令 |
| `Ctrl+O` | 切换详细输出模式（verbose） |
| `Ctrl+G` | 在默认文本编辑器中编辑当前提示 |
| `Ctrl+B` | 将当前运行的任务放到后台执行（tmux 用户需按两次） |
| `Ctrl+F` | 终止所有后台代理（3秒内按两次确认） |
| `Ctrl+T` | 切换任务列表显示 |
| `Ctrl+V` / `Cmd+V` | 从剪贴板粘贴图片 |
| `↑` / `↓` | 浏览输入历史 |
| `←` / `→` | 在对话框/菜单标签页间切换 |
| `Esc` + `Esc` | 回退（rewind）或从选定消息处总结 |
| `Shift+Tab` / `Alt+M` | 切换权限模式（Auto-Accept / Plan / Normal） |
| `Option+P` (Mac) / `Alt+P` | 快速切换模型（不清除当前输入） |
| `Option+T` (Mac) / `Alt+T` | 切换扩展思考模式（需先 `/terminal-setup`） |

### 2.3.2. 多行输入

| 方式 | 快捷键 | 适用环境 |
|------|--------|----------|
| 快捷转义 | `\` + `Enter` | 所有终端 |
| macOS 默认 | `Option+Enter` | macOS |
| Shift+Enter | `Shift+Enter` | iTerm2 / WezTerm / Ghostty / Kitty |
| 换行控制符 | `Ctrl+J` | 通用 |
| 粘贴模式 | 直接粘贴 | 适合代码块/日志 |

### 2.3.3. 快速触发

| 输入 | 功能 |
|------|------|
| `/` 开头 | 触发斜杠命令/技能 |
| `!` 开头 | 直接执行 Bash 命令（不经 Claude 解释） |
| `@` | 触发文件路径自动补全 |
| `#` 开头 | 快速添加记忆到 CLAUDE.md |

## 2.4. Vim 模式

使用 `/vim` 开启 Vim 模式后可用：

### 2.4.1. 模式切换

| 按键 | 功能 | 来源模式 |
|------|------|----------|
| `Esc` | 进入 NORMAL 模式 | INSERT |
| `i` | 光标前插入 | NORMAL |
| `I` | 行首插入 | NORMAL |
| `a` | 光标后插入 | NORMAL |
| `A` | 行末插入 | NORMAL |
| `o` | 在下方开新行 | NORMAL |
| `O` | 在上方开新行 | NORMAL |

### 2.4.2. 光标移动（NORMAL）

| 按键 | 功能 |
|------|------|
| `h`/`j`/`k`/`l` | 左/下/上/右移动 |
| `w` / `e` / `b` | 下一词 / 词尾 / 上一词 |
| `0` / `$` / `^` | 行首 / 行尾 / 首个非空字符 |
| `gg` / `G` | 文档首 / 文档尾 |
| `f{char}` / `F{char}` | 跳到下一个/上一个指定字符 |

### 2.4.3. 编辑操作（NORMAL）

| 按键 | 功能 |
|------|------|
| `x` | 删除字符 |
| `dd` / `D` | 删除整行 / 删除到行尾 |
| `dw`/`de`/`db` | 删除一个词/到词尾/到词首 |
| `cc` / `C` | 替换整行 / 替换到行尾 |
| `yy`/`Y` | 复制整行 |
| `p` / `P` | 在光标后/前粘贴 |
| `>>` / `<<` | 缩进/取消缩进 |
| `.` | 重复上一次操作 |

### 2.4.4. 文本对象（搭配 d/c/y 使用）

| 按键 | 功能 |
|------|------|
| `iw`/`aw` | 内部词/包含周围空格的词 |
| `i"`/`a"` | 双引号内/含双引号 |
| `i'`/`a'` | 单引号内/含单引号 |
| `i(`/`a(` | 小括号内/含小括号 |
| `i[`/`a[` | 中括号内/含中括号 |
| `i{`/`a{` | 大括号内/含大括号 |

## 2.5. 常用技巧

- **双击 ESC 键**：当代码被 Claude Code 改错时，可以双击 ESC，然后选择对应的对话记录，选择重新存储对应对话的代码记录即可

- **UltraThink 关键字**：当设计某个复杂任务时，在提示词中加上 `UltraThink` 关键字，会消耗更多的 token 和时间，但是代码质量会得到提升

- **Ctrl + S**：暂存提示词，它会自动保存提示词。适用场景：写长提示词时突然想起其他事，需要先查看某个文件再继续，临时被打断但不想丢失思路

# 3. 插件配合 Claude Code 开发探索

## 3.1. Claude Code SubAgents

### 3.1.1. github.com/wshobson/agents

> 73个专家智能体让vibe coding效率翻倍

在 Claude Code 对话框中安装：

```bash
/plugin marketplace add wshobson/agents
```

或者直接用这个 prompt 让 Claude Code 帮助我们安装：

```text
https://github.com/wshobson/agents 把这些Agents 全部安装到我的 ClaudeCode CLI里, 跳过重复的。
```

## 3.2. 自动化工作流

### 3.2.1. github.com/eyaltoledano/claude-task-master

> 一个面向使用 Claude 进行人工智能驱动开发的任务管理系统，旨在与 Cursor AI 无缝协作。

提示词（这里是直接把 agent 的权限下放给 Claude Code，让其充分使用）：

```text
用尽量多的agent 检查我们的项目,只检查,不修改代码。
```

## 3.3. OpenSpec

> 项目地址：[GitHub - Fission-AI/OpenSpec: Spec-driven development for AI coding assistants.](https://github.com/Fission-AI/OpenSpec)

参考：
- [OpenSpec — A lightweight spec‑driven framework](https://openspec.dev/)
- [OpenSpec：让 AI 编码助手更懂你的项目规范](https://blog.chensoul.cc/posts/2025/11/07/openspec/)

### 3.3.1. 安装 OpenSpec

```bash
npm install -g @fission-ai/openspec@latest
```

### 3.3.2. OpenSpec 使用

1. 进入到项目下，使用 OpenSpec 初始化项目：

```bash
# cd /path/to/your-project
openspec init
```

>**初始化过程中会发生什么：**
>
>1. 提示你选择使用的 AI 工具（选择 Claude Code）
>2. 自动配置 Claude Code 的斜杠命令（slash commands）
>3. 在项目根目录创建 `AGENTS.md` 文件
>4. 创建 `openspec/` 目录结构

2. 填充项目信息，对于 OpenSpec 来说，所有的项目信息都保存在 `openspec/project.md` 下，这里使用 Claude Code 填充项目信息，提示词如下：

```bash
请阅读 openspec/project.md 文件，帮我填写项目的技术栈、架构模式和编码规范
```

Claude Code 会分析你的项目并生成类似这样的内容：

```markdown
# Project Context

## Tech Stack
- **Backend**: Node.js 18, Express.js
- **Database**: PostgreSQL 14
- **Frontend**: React 18, TypeScript
- **Authentication**: JWT, bcrypt
- **Testing**: Jest, Supertest

## Architecture Patterns
- RESTful API design
- MVC pattern
- Repository pattern for data access
- Middleware-based request processing

## Coding Conventions
- Use TypeScript for type safety
- Follow Airbnb JavaScript style guide
- Use async/await for asynchronous operations
- Comprehensive error handling with custom error classes
- Write unit tests for all business logic
```

3. 需求开发示例：

在开发需求前，先补充说明下 OpenSpec 为 Claude Code 都安装了哪些插件功能：

| 命令 | 说明 |
|------|------|
| `/openspec:proposal` | 搭建一个新的 OpenSpec 变更并进行严格验证 |
| `/openspec:archive` | 归档已部署的 OpenSpec 变更并更新规范 |
| `/openspec:apply` | 实施已批准的 OpenSpec 变更并保持任务同步 |

## 3.4. superpowers - 测试驱动开发

> 项目地址：[GitHub - obra/superpowers: Claude Code superpowers: core skills library](https://github.com/obra/superpowers)

# 4. 使用技巧与最佳实践

TODO

# 参考文章

1. [Claude Code 概述 - Claude Code Docs](https://code.claude.com/docs/zh-CN)
2. [我花2天整理了GitHub上17个Claude Code优秀开源项目 | BadAGI.org](https://www.badagi.org/posts/claude-code-17-awesome-open-source-projects)
3. [生产级Claude Code SubAgents：73个专家智能体让vibe coding效率翻倍 | BadAGI.org](https://www.badagi.org/posts/claude-code-subagents-production-ready)