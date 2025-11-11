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

# 参考资料
- [GitHub - hesreallyhim/awesome-claude-code: A curated list of awesome commands, files, and workflows for Claude Code](https://github.com/hesreallyhim/awesome-claude-code