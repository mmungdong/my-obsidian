---
title: AI Coding - Claude code 使用笔记
date: 2025-11-11
categories:
  - 代码笔记
tags:
  - AI
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
> 🔗 [ZCF - Zero-Config Code Flow \| ZCF](https://zcf.ufomiao.com/zh-CN/)

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

# 2. 插件配合 Claude code 开发探索

## 2.1. Claude Code SubAgents

### 2.1.1. github.com/wshobson/agents

>  73个专家智能体让vibe coding效率翻倍

在 Claude Code 对话框中安装：

```bash
/plugin marketplace add wshobson/agents
```

或者zhi
## 2.1. OpenSpec

> 项目地址： [GitHub - Fission-AI/OpenSpec: Spec-driven development for AI coding assistants.](https://github.com/Fission-AI/OpenSpec)

参考：
- [OpenSpec — A lightweight spec‑driven framework](https://openspec.dev/)
- [OpenSpec：让 AI 编码助手更懂你的项目规范 - Java、Spring、Spring Boot、MicroServices、Architecture、Kubernetes、DevOps](https://blog.chensoul.cc/posts/2025/11/07/openspec/)

### 2.1.1 安装 OpenSpec

```bash
npm install -g @fission-ai/openspec@latest
```

### 2.1.2 OpenSpec 使用

1. 进入到项目下，使用 OpenSpec 初始化项目：

```bash
# cd /path/to/your-project
openspec init
```

>**初始化过程中会发生什么：**
>
>1. 提示你选择使用的 AI 工具（选择 Claude Code）
>2. 自动配置 Claude Code 的斜杠命令（slash commands）
>3. 在项目根目录创建 `AGENTS.md` 文件
>4. 创建 `openspec/` 目录结构

2. 填充项目信息，对于 OpenSpec 来说，所有的项目信息都保存在 `openspec/project.md` 下，这里使用 Claude Code 填充下项目要信息，提示词如下：

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

 > /openspec:proposal     Scaffold a new OpenSpec change and validate strictly. (project) [搭建一个新的 OpenSpec 变更并进行严格验证]
 > /openspec:archive      Archive a deployed OpenSpec change and update specs. (project) [归档已部署的 OpenSpec 变更并更新规范]
 > /openspec:apply        Implement an approved OpenSpec change and keep tasks in sync. (project) [实施已批准的 OpenSpec 变更并保持任务同步]


TODO


## 2.2. superpowers - 使用 Claude Code 进行测试驱动开发

> 项目地址：[GitHub - obra/superpowers: Claude Code superpowers: core skills library](https://github.com/obra/superpowers)


