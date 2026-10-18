---
title: AI Agent 工程师 / FDE 学习计划
date: 2026-10-18 14:31:16
categories:
  - 代码笔记
tags:
  - AI
  - Agent
  - LLM
  - 学习路线
  - FDE
archive: false
hide: false
---

> 目标：能独立承担「Agent 开发」与「FDE（Forward Deployed Engineer）」两类岗位。
> 周期：12 周，每周 10–12 小时。
> 原则：**70% 动手，30% 读文档**。视频只用来建立直觉，不用来学细节；细节一律回官方文档。

---

# 1. 能力地图

| 层 | 要掌握的东西 | 对应岗位价值 |
|---|---|---|
| **A. 模型层** | Token / 上下文窗口 / 采样参数 / Prompt 结构 / Function Calling / Structured Output | 所有岗位的地基 |
| **B. Agent 工程层** | 编排（ReAct、Plan-Execute、状态机）、工具与 MCP、RAG、Memory、多 Agent | Agent 开发的核心 |
| **C. 生产化层** | 评估体系、可观测性、Runtime（重试/幂等/断点）、安全边界、部署 | 区分「Demo」与「能上线」 |
| **D. FDE 交付层** | 全栈原型、私有化部署、企业系统集成、数据工程、需求转译、交付文档 | FDE 的分水岭 |

A→B→C 是深度，D 是广度。两类岗位的差异：Agent 开发岗 C 层要求深，FDE 岗 D 层要求广且能独立扛客户。

---

# 2. 十二周计划总表

| 周 | 主题 | 主要资料类型 | 交付物（不会做=没学会） |
|---|---|---|---|
| W1 | LLM 基础 + 提示工程 | 官方文档 + 长文 | 一份 30 条用例的 Prompt 迭代记录 |
| W2 | Structured Output + Function Calling | 官方文档 | 带 Schema 校验与重试的结构化输出模块 |
| W3 | Agent 编排原理 + LangGraph | 官方教程 + 视频 | 手写 ReAct loop + LangGraph 版对照 |
| W4 | Agent Runtime（状态/重试/幂等/恢复） | 文档 + 源码 | 可断点续跑的任务执行器 |
| W5 | MCP 协议 + 工具设计 | 官方规范 | 3 个自建 MCP Server |
| W6 | Agent 安全 | OWASP + 实践 | 工具授权矩阵 + 20 条注入样本测试集 |
| W7 | RAG 全链路 | 官方文档 | 混合检索 + Rerank 的消融对比表 |
| W8 | Context Engineering | 长文 + 实践 | 动态上下文装配器 + 证据溯源 |
| W9 | 评估体系（离线） | 三个评估框架文档 | 金标集 + Precision/Recall/漏检率报告 |
| W10 | 评估体系（CI 回归）+ 可观测 | 文档 + 实践 | CI 里的 eval pipeline + trace 看板 |
| W11 | 多 Agent 协作 | 官方教程 + 工程博客 | Supervisor + Agent-as-Tool 流水线 |
| W12 | FDE 专项：全栈 + 部署 + 交付 | 框架文档 | 一套可交付的私有化原型 + 交付文档包 |

---

# 3. 分阶段详解

## 3.1. W1 — LLM 基础与提示工程

**要掌握**
- Token 化与中文 token 效率、上下文窗口的真实限制、KV cache 与成本模型
- 采样参数（temperature / top_p / max_tokens）各自影响什么
- 提示结构：角色设定、指令层次、few-shot、输出格式约束、思维链的适用边界
- 幻觉的成因分类：知识缺失、指令冲突、格式压力、长上下文干扰

**资料**
- 文档：OpenAI Cookbook — <https://cookbook.openai.com>
- 文档：Anthropic Prompt Engineering 指南 — <https://docs.claude.com>（找 Prompt engineering 章节）
- 文档：Google Gemini Prompting Strategies — <https://ai.google.dev/gemini-api/docs/prompting-strategies>
- 视频：Andrej Karpathy《Intro to Large Language Models》《Deep Dive into LLMs like ChatGPT》（YouTube，频道 Andrej Karpathy）
- 视频：DeepLearning.AI 短课程（官网免费，选 Prompt Engineering 相关）

**练习**：挑一个你熟悉的业务任务，写 5 版 Prompt，每版记录改动点与失败案例，形成对照表。

---

## 3.2. W2 — Structured Output 与 Function Calling

**要掌握**
- Function Calling 的协议流程：模型返回 tool_call → 应用执行 → 回填 tool_result
- Structured Output 两种实现：JSON Schema 约束 vs 约束解码（constrained decoding）
- Schema 设计原则：扁平优于嵌套、枚举优于自由文本、必填项最小化
- 输出不合法时的三层处理：Schema 校验 → 业务校验 → 分级重试/降级
- Pydantic 做类型模型 + 校验器 + 依赖注入

**资料**
- 文档：OpenAI Structured Outputs / Function Calling — <https://platform.openai.com/docs>
- 文档：Anthropic Tool Use — <https://docs.claude.com>
- 文档：Pydantic AI 官方文档（重点看 Output 与 Dependencies 两章）— <https://ai.pydantic.dev>
- 视频：YouTube 搜 `structured outputs json schema`、`function calling tutorial`

**练习**：定义一个含 5 个字段、2 个枚举、1 个业务校验规则的结果模型，跑 200 次统计非法率并调优到 <1%。

---

## 3.3. W3 — Agent 编排原理与 LangGraph

**要掌握**
- ReAct 循环的本质与失效场景（长链条绕圈、工具误选）
- Plan-and-Execute 与 ReAct 的取舍
- 状态机式编排：State 设计、节点/边、条件路由、循环与退出条件
- LangGraph 核心：StateGraph、Checkpointer、Interrupt、Subgraph、并发分支
- 框架对比：LangGraph / LlamaIndex Workflows / Pydantic AI / OpenAI Agents SDK 各自的抽象

**资料**
- 文档：LangChain / LangGraph 官方文档（Quickstart → Persistence → Human-in-the-loop → Subgraphs 依次读）— <https://docs.langchain.com>
- 文档：OpenAI Agents SDK（Agents / Handoffs / Guardrails / Tracing 四章）— <https://openai.github.io/openai-agents-python/>
- 文档：LlamaIndex Workflows — <https://docs.llamaindex.ai>
- 长文：Anthropic《Building Effective Agents》（必读，讲 workflow 与 agent 的边界）
- 视频：YouTube 频道 **LangChain**（官方 LangGraph 系列）
- 视频：YouTube 频道 **IndyDevDan**、**Sam Witteveen**（LangGraph / Agent 实战）
- 视频：B 站检索 `LangGraph 教程`、`LangChain 入门`（UP 主「同济子豪兄」有系统中文教程系列）

**练习**：先**不用框架**手写一个 30 行的 ReAct loop（while + tool dispatch），再用 LangGraph 重写，对比两者在错误处理上的差异。

---

## 3.4. W4 — Agent Runtime（生产化执行）

**要掌握**
- 状态持久化：checkpoint 存什么、存哪里、多久
- 幂等：任务级幂等键设计，重复提交不重复执行
- 超时与重试：指数退避、熔断阈值、"重试换条件"原则
- 断点恢复：第 N 步失败后从第 N 步继续，不重跑全流程
- 任务取消：正在飞的模型调用如何被打断
- 并发控制：限流、队列、优先级
- 长任务模式：durable execution 的概念（Temporal 解决什么问题）

**资料**
- 文档：LangGraph Persistence / Checkpointer 章节 — <https://docs.langchain.com>
- 文档：Temporal 概念篇（只读 Concepts，不必上手）— <https://docs.temporal.io>
- 文档：Redis / PostgreSQL 官方文档（队列与事务相关章节）
- 视频：YouTube 搜 `durable execution temporal`、`agent checkpoint resume`

**练习**：让任务在执行中途被 `kill -9`，重启后从断点继续，且不重复产生副作用。

---

## 3.5. W5 — MCP 与工具设计

**要掌握**
- MCP 三类原语：Tools / Resources / Prompts
- 传输方式：stdio 与 HTTP+SSE 的适用场景
- Server 的注册、发现、鉴权
- Tool Schema 设计：描述怎么写模型才不会误用、参数怎么约束
- MCP 与 Function Calling 的关系（协议 vs 调用机制，不要混为一谈）

**资料**
- 文档：MCP 官方规范 — <https://modelcontextprotocol.io>
- 文档：Anthropic MCP 文档与发布公告 — <https://docs.claude.com>
- 文档：各类 MCP Server 示例仓库（GitHub 搜 `modelcontextprotocol servers`）
- 视频：YouTube 搜 `MCP server tutorial`、频道 **Anthropic** 的 MCP 讲解

**练习**：写 3 个 Server —— 文件检索、HTTP API 取数、数据库查询，各用一种传输方式。

---

## 3.6. W6 — Agent 安全

**要掌握**
- 直接注入 vs **间接注入**（文档/网页/邮件里藏指令）——后者才是生产环境主威胁
- 工具滥用、权限越界、上下文污染、多 Agent 通信劫持
- 防御必须在应用层：工具白名单 × 角色 × 参数范围、路径白名单、只读默认
- 沙箱：进程隔离、网络出口控制、文件系统挂载限制
- 审计日志：谁、在哪个任务、调了什么、拿到什么
- 最小权限原则在 Agent 上的落地方式

**资料**
- 文档：OWASP Top 10 for LLM Applications — <https://genai.owasp.org>
- 文档：OWASP Agentic AI 威胁清单（重点：工具滥用、身份权限滥用、上下文污染）
- 长文：Anthropic / OpenAI 关于 prompt injection 的安全说明
- 视频：YouTube 搜 `prompt injection defense`、`LLM agent security`

**练习**：构造 20 条注入样本（含伪装成正常业务文档的），验证全部在工具执行前被拦截。

---

## 3.7. W7 — RAG 全链路

**要掌握**
- Embedding 选型：中文业务文档不要直接套英文模型，要看 MTEB 中文榜
- 分块策略：条款按条款切、合同按章节切，不要按固定字数切
- 向量库：pgvector 起步够用，Milvus/Qdrant 何时才需要
- 混合检索：BM25 + 向量，各自补对方的什么短板
- Rerank：为什么召回 20 条再重排比直接取 top3 好
- 检索质量评估：Hit Rate、MRR、Context Precision / Recall

**资料**
- 文档：LlamaIndex（Loading → Indexing → Retrieval → Reranking）— <https://docs.llamaindex.ai>
- 文档：pgvector README — <https://github.com/pgvector/pgvector>
- 文档：Ragas（RAG 评估指标）— <https://docs.ragas.io>
- 视频：YouTube 频道 **Data Independent**（RAG 系列讲得很扎实）
- 视频：B 站检索 `RAG 实战`、`向量数据库 pgvector`

**练习**：同一批问题做四组消融——纯向量 / 纯 BM25 / 混合 / 混合+Rerank，出准确率对比表。

---

## 3.8. W8 — Context Engineering

**要掌握**
- 核心命题不是「怎么检索」，而是「这一步模型该看到什么」
- 四件事：Retrieval（按任务取）、Selection（相关度筛）、Compression（压缩保关键）、Memory（不重复喂）
- 上下文装配：按任务类型动态加载不同资料组合
- 长文档处理：分页摘要 + 关键段保留 + 原文指针
- Token 预算分配与干扰控制（无关信息会降低准确率）
- 证据溯源：每条结论能指回原文位置

**资料**
- 长文：Anthropic《Effective Context Engineering for AI Agents》
- 长文：LangChain 官方博客的 Context Engineering 系列
- 文档：Anthropic Long Context 相关文档 — <https://docs.claude.com>
- 视频：YouTube 搜 `context engineering agents`

**练习**：做 A/B —— 全量塞入 vs 装配后塞入，比较 token 消耗与准确率，两个指标都要记录。

---

## 3.9. W9 — 评估体系（离线）

**要掌握**
- 金标集怎么建：抽样策略、标注规范、严重程度分级
- 核心指标：Precision（报了的问题有多少真）、Recall（真问题发现了多少）、漏检率
- **按严重程度分层算**，重大风险和格式问题不能混在一起看
- LLM-as-a-Judge：评分标准怎么写、位置偏见怎么消除、与人工标注的一致性怎么验（用 Cohen's kappa）
- RAG 侧指标与 Agent 侧指标的分工

**资料**
- 文档：DeepEval — <https://docs.confident-ai.com>
- 文档：Ragas — <https://docs.ragas.io>
- 文档：promptfoo（回归与红队测试）— <https://www.promptfoo.dev/docs/>
- 文档：LangSmith 评估章节 — <https://docs.smith.langchain.com>
- 视频：YouTube 频道 **Confident AI**（DeepEval 官方）、搜 `LLM evaluation ragas`

**练习**：建 50 条金标样本，跑出三种严重程度各自的 P/R/漏检率，并与人工复核对齐。

---

## 3.10. W10 — 评估回归与可观测性

**要掌握**
- 把 eval 挂进 CI：每次改 Prompt / 换模型自动出对比报告
- 回归测试的通过阈值怎么定（不是"不能下降"，而是"关键用例不能下降"）
- Trace 结构设计：一次请求 = 一个 trace，每个环节 = span，每次模型调用带 token 与耗时
- 成本归因：钱花在哪个环节、哪类任务最贵
- 告警：错误率、P99 延迟、单次成本突增
- OpenTelemetry GenAI 语义约定

**资料**
- 文档：Langfuse — <https://langfuse.com/docs>
- 文档：OpenTelemetry GenAI Semantic Conventions — <https://opentelemetry.io/docs/specs/semconv/gen-ai/>
- 文档：LangSmith 在线评估与监控章节 — <https://docs.smith.langchain.com>
- 视频：YouTube 搜 `LLM observability langfuse`

**练习**：把模型 A 换成模型 B，一条命令生成「哪些用例变好、哪些变差」的对比报告。

---

## 3.11. W11 — 多 Agent 协作

**要掌握**
- 三种模式：Supervisor（中心调度）/ Handoff（对等移交）/ Agent-as-Tool（子 Agent 当工具）
- 各自的可控性与可追踪性代价
- 通信契约：子 Agent 的输入输出怎么定，避免自由文本传递
- 失败传播：子 Agent 失败时降级还是中断
- 结论冲突裁决
- **什么时候不该用多 Agent**（很多场景单 Agent + 好工具更强）

**资料**
- 长文：Anthropic《How We Built Our Multi-Agent Research System》
- 文档：LangGraph Multi-Agent 教程（Supervisor / Swarm / Hierarchical）— <https://docs.langchain.com>
- 文档：OpenAI Agents SDK Handoffs 章节 — <https://openai.github.io/openai-agents-python/>
- 视频：YouTube 频道 **LangChain** 的 multi-agent 系列

**练习**：搭「初审 Agent + 复核 Agent + 汇总 Supervisor」，并对比单 Agent 版本的质量与成本。

---

## 3.12. W12 — FDE 专项

这一周是 FDE 岗位的真正考点，也是大部分 Agent 开发者的短板。

**要掌握**
- **全栈原型**：Next.js + FastAPI（或 Go），流式输出、会话管理
- **私有化部署**：Docker Compose → K8s、离线镜像、内网依赖源、等保基本要求
- **企业系统集成**：企业微信 / 钉钉 / 飞书开放平台、SAP / 用友 / 金蝶 接口模式、数据库直连
- **数据工程**：SQL 进阶、ETL、脏数据清洗（客户现场的真实工作量在这里）
- **需求转译**：模糊诉求 → 用例 → 成功指标 → POC → 试点 → 上量
- **交付物**：方案书、部署手册、SOP、培训材料
- **软技能**：客户沟通、现场演示、英语（出海岗位常要求）

**资料**
- 文档：Next.js 官方教程 — <https://nextjs.org/learn>
- 文档：Vercel AI SDK（流式与 tool calling 集成）— <https://ai-sdk.dev>
- 文档：FastAPI — <https://fastapi.tiangolo.com>
- 文档：Docker / Kubernetes 官方教程
- 文档：企业微信开放平台、钉钉开放平台、飞书开放平台（各选一个完成对接 demo）
- 文档：Docling（PDF/文档解析）— <https://docling-project.github.io/docling/>
- 文档：PyMuPDF — <https://pymupdf.readthedocs.io>
- 长文：搜索 Palantir FDE 相关公开文章，理解这个岗位的原始定义

**练习**：把一个内部 Agent 做成「客户能自己装起来」的交付包——一条命令部署、一份部署手册、一份 30 分钟培训材料。

---

# 4. 资源总表

## 4.1. 官方文档（按优先级）

| 优先级 | 文档                             | 用途                                |
| --- | ------------------------------ | --------------------------------- |
| P0  | LangGraph / LangChain          | 主编排框架                             |
| P0  | MCP 官方规范                       | 工具协议                              |
| P0  | OpenAI Agents SDK              | 对照 handoff / guardrails / tracing |
| P0  | Pydantic AI                    | 结构化输出与类型安全                        |
| P0  | OWASP GenAI / Agentic AI       | 安全清单                              |
| P1  | LlamaIndex                     | RAG 与检索                           |
| P1  | Ragas / DeepEval / promptfoo   | 评估                                |
| P1  | Langfuse / OpenTelemetry GenAI | 可观测                               |
| P1  | Anthropic 工程博客                 | 三篇必读长文                            |
| P2  | Vercel AI SDK / Next.js        | FDE 全栈                            |
| P2  | Temporal Concepts              | 理解 durable execution（不必上手）        |
| P3  | vLLM / Dify / Coze             | 按需，不列入主线                          |

## 4.2. 视频资源

**YouTube（英文，质量更稳）**

| 频道 | 看什么 |
|---|---|
| Andrej Karpathy | LLM 原理，建立直觉 |
| LangChain | LangGraph 官方教程、multi-agent |
| Anthropic | MCP、Agent 设计理念 |
| OpenAI | Agents SDK、structured outputs |
| DeepLearning.AI | 免费短课程，快速过一遍概念 |
| IndyDevDan | Agent / LangGraph 实战 |
| Sam Witteveen | Agent 框架对比与实战 |
| Data Independent | RAG 系列 |
| Confident AI | DeepEval 评估实践 |

**B 站（中文，用来降低入门门槛）**

以检索关键词为主，不建议追单一 UP 主：

- `LangGraph 教程` / `LangChain 入门`
- `MCP 协议 讲解`
- `RAG 实战` / `向量数据库 pgvector`
- `Agent 开发 实战`
- `大模型 结构化输出`

> 中文教程质量参差，遇到说法冲突一律以官方文档为准。UP 主「同济子豪兄」有较系统的中文 LLM 应用教程系列，可作为入门补充。

## 4.3. 必读长文（四篇，都不长，但决定认知高度）

1. Anthropic《Building Effective Agents》—— workflow 与 agent 的边界
2. Anthropic《Effective Context Engineering for AI Agents》—— 上下文工程
3. Anthropic《How We Built Our Multi-Agent Research System》—— 多 Agent 的真实代价
4. OpenAI《A Practical Guide to Building Agents》—— 工程落地清单

---

# 5. 时间与节奏

**每周 10–12 小时分配**

| 内容 | 时长 | 说明 |
|---|---|---|
| 读官方文档 | 2–3h | 只读当前阶段需要的章节，不要通读 |
| 写代码 | 6–7h | 必须是真实项目代码，不是玩具 demo |
| 看视频 | 1h | 只用来建立直觉，不做笔记 |
| 写复盘 / 决策记录 | 1h | 记录「为什么选 A 不选 B」 |

**学习方法三条**

1. **官方文档优先于视频**。视频看 2 倍速，卡住立刻去翻文档。
2. **每个阶段必须有交付物**。计划表右列做不到，就说明这个阶段没学会，不要往下走。
3. **写决策记录**。每周半页：选了什么、为什么、什么条件下会推翻。这是面试时最能体现深度的材料。

---

# 6. 验收标准（自测）

学完后你能回答这些问题，才算过关：

- [ ] 为什么 ReAct 在长链条任务上会退化？什么时候该换 Plan-and-Execute？
- [ ] LangGraph 的 checkpointer 存的是什么？断点恢复时哪些状态不能被重放？
- [ ] MCP 和 Function Calling 分别解决什么问题？为什么不是二选一？
- [ ] 间接注入和直接注入的防御策略有何不同？为什么 Prompt 层防御不够？
- [ ] 混合检索里 BM25 补的是向量的什么短板？Rerank 的收益来自哪里？
- [ ] 你的 Agent 上线后，用什么指标判断「这次改动变好了」？P/R/漏检率分别怎么算？
- [ ] LLM-as-a-Judge 的分数为什么不能直接当真理？怎么验证它可信？
- [ ] 任务执行到第 7 步失败，如何恢复？哪些操作必须幂等？
- [ ] 多 Agent 相比单 Agent + 好工具，什么情况下才值得？
- [ ] 客户现场数据是脏的、系统是老的、网络是内网的，你的方案怎么落地？

---

# 7. 明确不学的（避免分散精力）

| 项 | 原因 |
|---|---|
| LoRA / SFT / 量化 / 蒸馏 | 没有评估基线之前做微调，无法判断好坏。放到学完 W9 之后按需 |
| vLLM 自部署 | 除非有私有化刚需或明确成本压力，先用 API |
| CrewAI / AutoGen | 读设计思想即可，不必上手。企业场景要可审计的固定流程 |
| Dify / Coze 深入 | FDE 岗要求会用（能快速搭 POC），但不要当成技术深度 |
| Prompt 花式技巧 | 收益递减，把精力放在上下文工程和评估上 |
