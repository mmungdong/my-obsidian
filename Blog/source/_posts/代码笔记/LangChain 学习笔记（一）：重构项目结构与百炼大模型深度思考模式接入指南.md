---
title: LangChain 学习笔记（一）：重构项目结构与百炼大模型深度思考模式接入指南
date: 2026-03-03
categories:
  - 代码笔记
tags:
  - AI
  - LangChain
archive: false
hide: false
---

## 一、项目结构重构
在接入大模型之前，合理的项目结构是保证代码可维护性的基础。我们将原本杂乱的扁平结构，重构为了功能分明的模块化结构。
### 1.1 重构前后对比
**重构前（扁平结构，容易越写越乱）：**
```text
langchain-demo/
├── env_utils.py
├── my_llm.py
├── test_llm/
│   ├── __init__.py
│   └── test01.py
└── .env
```
**重构后（模块化结构，适合工程化）：**
```text
langchain-demo/
├── config/                    # 核心配置模块
│   ├── __init__.py
│   └── env_utils.py           # 环境变量加载工具
├── llms/                      # LLM 提供者统一封装层
│   ├── __init__.py
│   └── bailian.py             # 阿里云百炼平台 LLM 封装
├── tests/                     # 单元测试与效果测试目录
│   ├── __init__.py
│   └── test_llm_basic.py      # LLM 基础通信测试
├── .env                       # 环境变量（存放本地真实 Key，不可提交）
├── .env.example               # 环境变量模板（供他人参考，可提交）
├── .gitignore                 # Git 忽略规则（必加 .env）
├── README.md                  # 项目说明文档
└── requirements.txt           # 项目依赖列表
```
### 1.2 重构收益与要点
- **依赖隔离**：完善 `requirements.txt`，保证项目换电脑也能一键跑通。
- **信息安全**：添加 `.gitignore` 并忽略 `.env`，彻底杜绝了 API Key 泄露到 GitHub 的风险。
- **代码复用**：将零散的测试脚本封装为了规范的函数调用，统一了全局配置入口。
---
## 二、LangChain 调用百炼平台 API
阿里云百炼平台提供了极好的 **OpenAI 兼容接口**。这意味着我们在 LangChain 中，不需要去找特定的阿里云集成包，直接借用 `langchain_openai.ChatOpenAI` 即可完美调用。
### 2.1 环境变量配置（统一规范）
建议统一使用阿里云百炼官方文档推荐的命名：
```python
# config/env_utils.py
import os
from dotenv import load_dotenv
# override=True 确保以 .env 文件为准覆盖系统默认环境变量
load_dotenv(override=True)
# 统一使用百炼官方规范的命名
DASHSCOPE_API_KEY = os.getenv("DASHSCOPE_API_KEY")
DASHSCOPE_BASE_URL = os.getenv("DASHSCOPE_BASE_URL", "https://dashscope.aliyuncs.com/compatible-mode/v1")
```
### 2.2 基础模型实例化
```python
from langchain_openai import ChatOpenAI
from config.env_utils import DASHSCOPE_API_KEY, DASHSCOPE_BASE_URL
llm = ChatOpenAI(
    model="qwen-max",                     # 替换为你实际使用的模型名称（如 qwen-max, deepseek-r1 等）
    temperature=0.7,    api_key=DASHSCOPE_API_KEY,            # 百炼 API Key    base_url=DASHSCOPE_BASE_URL           # 务必指定百炼的 OpenAI 兼容端点
)
```
### 2.3 🚨 踩坑预警：千万别用 `ChatDeepSeek` 调百炼的 DeepSeek
如果你想调用百炼平台上托管的 `deepseek-r1` 等模型，**千万不要**这样做：
```python
from langchain_deepseek import ChatDeepSeek
# ❌ 这样会报错：Authentication Fails 或直接连接失败
deep_think_llm = ChatDeepSeek(
    model="deepseek-r1",    api_key=DASHSCOPE_API_KEY,base_url=DASHSCOPE_BASE_URL      )
```
💡 **根本原因：** `ChatDeepSeek` 是 LangChain 专门为 **DeepSeek 官方 API** 设计的类，其底层网络请求认证机制和请求头与阿里云平台并不完全兼容。
**正解：** 只要是调用阿里云百炼（无论什么模型），一律使用通用且协议最标准的 `ChatOpenAI` 并修改 `base_url` 即可。
---
## 三、深度思考模式的正确打开方式（核心重点）
当前具备“深度思考（CoT）”能力的大模型越来越火，在百炼平台中，部分模型可以通过参数手动开启思考模式。
### 3.1 如何开启深度思考？
通过 `extra_body` 将专属参数传递给底层 API：
```python
deep_think_llm = ChatOpenAI(
    model="qwen-max-latest",              # 注：需使用支持思考模式的模型
    temperature=0.7,    api_key=DASHSCOPE_API_KEY,    base_url=DASHSCOPE_BASE_URL,    extra_body={"enable_thinking": True}  # 🔥 关键参数：开启深度思考
)
```
### 3.2 为什么必须用流式（Stream）输出？
**这是一个硬性限制！** 对于开启了 `enable_thinking=True` 的模型：
- ❌ 调用 `invoke()`：非流式调用不仅会**丢失**思考过程，在百炼的某些 API 版本中甚至会**直接报错**（提示你必须关闭 thinking 或者改用流式）。
- ✅ 调用 `stream()`：流式调用可以实时且完整地拿到 `reasoning_content`（思考链）。
### 3.3 实战：流式获取思考与回复的完整代码
在 LangChain 中获取非标准的响应字段，必须利用 `additional_kwargs`：
```python
def test_deep_think_llm_stream():
    """测试深度思考模型（流式获取思考过程与最终结果）"""
    reasoning_content = ""  # 收集思考过程
    answer_content = ""     # 收集最终回复
    is_answering = False    # 状态标志：是否已进入正式回复阶段
        print("\n" + "🤔" * 10 + " 思考过程 " + "🤔" * 10 + "\n")
    # 必须使用 .stream()    for chunk in deep_think_llm.stream("用三句话简单介绍下AI agent"):
        # 【核心技巧】LangChain 会将非 OpenAI 标准的字段兜底放在 additional_kwargs 中
        chunk_reasoning = chunk.additional_kwargs.get("reasoning_content", "")                if chunk_reasoning:
            reasoning_content += chunk_reasoning            print(chunk_reasoning, end="", flush=True)            # 当获取到正常的内容字段时，说明思考结束，开始正式回答
        if chunk.content:            if not is_answering:                print("\n\n" + "💡" * 10 + " 最终回复 " + "💡" * 10 + "\n")
                is_answering = True                            answer_content += chunk.content
            print(chunk.content, end="", flush=True)                return reasoning_content, answer_content
```
### 3.4 Token 消耗的隐藏细节（💳 计费预警）
仔细观察返回的响应元数据（Response Metadata），你会发现计费维度的变化：
- **普通对话**：`completion_tokens`（补全输出量）
- **深度思考对话**：新增了 `reasoning_tokens`。
  > **注意**：大模型“自言自语”的思考过程通常会非常长（动辄上千 Token），这部分是被归入输出计费的！在设计 C 端商业产品时，一定要考虑这部分产生的额外算力成本。
---
## 四、原始 API 调用对比（知其然，知其所以然）
为了搞明白 LangChain 底层干了什么，我们可以对比一下使用纯粹的 `openai` SDK 是怎么调用的：
```python
import os
from openai import OpenAI
client = OpenAI(
    api_key=os.getenv("DASHSCOPE_API_KEY"),    base_url="https://dashscope.aliyuncs.com/compatible-mode/v1",)
completion = client.chat.completions.create(
    model="qwen-max-latest",    messages=[{"role": "user", "content": "你是谁"}],
    extra_body={"enable_thinking": True},    stream=True,  # 强制要求流式
)
for chunk in completion:
    delta = chunk.choices[0].delta    # 原始 API 可以通过点运算符直接访问 reasoning_content    if hasattr(delta, "reasoning_content") and delta.reasoning_content:        print(delta.reasoning_content, end="")            if hasattr(delta, "content") and delta.content:
        print(delta.content, end="")
```
**底层逻辑映射总结：**
- 原始 SDK 中的 `delta.reasoning_content` 👉 等价于 LangChain 封装后的 `chunk.additional_kwargs.get("reasoning_content")`。LangChain 帮我们抹平了不同底层的差异，但遇到新特性时，多翻翻 `additional_kwargs` 准没错！
---
## 五、最佳实践复盘
1. **项目隔离与解耦**：坚持配置与业务逻辑分离，通过 `.env` 管理敏感凭证，这是工程师的基本素养。
2. **"万能插座"法则**：面对提供了“OpenAI兼容接口”的国产大模型平台，`ChatOpenAI` 就是最好的“万能插座”，别乱用专用 SDK 惹出一堆兼容性报错。
3. **理解大模型流式思考**：未来的大模型（如 O1、DeepSeek R1）必将标配思考过程。遇到思考过程拿不到的问题：**一上 Stream，二查 additional_kwargs**。
---
## 六、延伸阅读与参考资料
- [阿里云百炼平台官方文档 - OpenAI 兼容调用](https://help.aliyun.com/zh/model-studio/developer-reference/use-qwen-by-calling-api)
- [LangChain 官方文档 - ChatOpenAI](https://python.langchain.com/docs/integrations/chat/openai/)
- [DeepSeek 官方提示词与用法参考](https://api-docs.deepseek.com/zh-cn/)