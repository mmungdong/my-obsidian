
# 1. 安装

- [请稍候…](https://www.npmjs.com/package/@openai/codex)

需要预先安装 npm 环境，执行 `npm install -g @openai/codex` 安装 codex

# 2. 配置

> 参考：
> 1. [codex/docs/config.md at main · openai/codex · GitHub](https://github.com/openai/codex/blob/main/docs/config.md)
> 2. [codex/docs/example-config.md at main · openai/codex · GitHub](https://github.com/openai/codex/blob/main/docs/example-config.md)

默认配置文件位置： **~/.codex/config.toml**

最小化可用配置（以阿里云百炼为例）：

```toml
model = "Moonshot-Kimi-K2-Instruct"
model_provider = "bailian"

[model_providers.bailian]
name = "bailian"
base_url = "https://dashscope.aliyuncs.com/compatible-mode/v1"
env_key = "ALIYUN_BAILIAN_API_KEY"
wire_api = "chat"
```
