
# 1. 安装

## 1.1. 下载

```shell
npm install -g @anthropic-ai/claude-code
```

## 1.2. 配置

以 wsl 为例，在 ~/.bashrc 中添加如下配置即可：

```shell
# claude code
# 百炼 API EP
export ANTHROPIC_BASE_URL=https://dashscope.aliyuncs.com/apps/anthropic
export ANTHROPIC_AUTH_TOKEN=sk-**************
# model id
export ANTHROPIC_MODEL=qwen3-coder-plus
```

