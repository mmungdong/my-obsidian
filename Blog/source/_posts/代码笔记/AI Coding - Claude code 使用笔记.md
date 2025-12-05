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
# 百炼 API EP
export ANTHROPIC_BASE_URL=https://dashscope.aliyuncs.com/apps/anthropic
export ANTHROPIC_AUTH_TOKEN=sk-**************
# model id
export ANTHROPIC_MODEL=qwen3-coder-plus
```

# 2. 插件配合 Claude code 开发探索

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


