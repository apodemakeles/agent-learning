# Phase 3.5 · 企业 RAG 与 Agentic Retrieval（Week 13–14）

> 目标：把"agent + 企业知识库"这个 95% 的真实业务需求系统学一遍。从经典 RAG 工程（chunking / 多源检索 / 重排序 / 评估）到 agentic RAG（agent 自己决定检索什么、什么时候检、要不要再检一轮），最后用 TS 做一个**有评估指标**的小型企业 RAG agent。

---

## 本阶段定位（30 秒读完）

- **不是 RAG 子领域速成班**：不会让你成为 RAG 专家。这两周专注于"agent 开发者必须懂的 RAG"
- **是 agent + RAG 的结合点**：重点在"agentic retrieval"——把 retrieval 当成 agent 的一个 tool / 一种行为，而非外置的"先查再答"管线
- **TS 优先**：用 LangChain.js / Mastra / 原生实现，Python 框架（LlamaIndex 等）只读不重写

**为什么要单独学一周半**：
- 企业场景下，几乎所有"通用业务 agent"最终都要接知识库
- "RAG 是 agent 的工具"这个视角在大部分 RAG 教程里是缺失的——它们仍把 RAG 当独立管线讲
- 评估、权限、幻觉控制——这三块是企业 RAG 的真正难点，恰好也是 agent 工程的通用能力

---

## 前置条件与时间预算

- **前置**：Phase 3 自检全过；`mini-agent-v2` 能用；理解 tool calling
- **时间**：Week 13–14，每周 6h，合计 ≈ 12h（可弹性扩到 16h）
- **交付**：
  - `notes/phase-3.5/rag-concepts.md`：RAG / agentic RAG 概念笔记
  - `notes/phase-3.5/enterprise-rag-agent/`：能跑的 TS 企业 RAG agent
  - `notes/phase-3.5/rag-evals/`：用 RAGAS（或自写 grader）的评估套件，≥10 条问答对

---

## 学习目标

1. 能说出经典 RAG 管线和 agentic RAG 的核心差异，并知道各自适用场景
2. 能解释 chunking / embedding / 多源检索 / 重排序 / 引用追溯五个环节里**最容易出问题的两个**
3. 能用 TS 搭一个支持"agent 自主决定查不查、查几轮"的 agentic RAG
4. 能用 RAGAS 或等价方案给 RAG 系统打分，理解 faithfulness / answer relevancy / context precision 三个核心指标
5. 知道企业级 RAG 的三个常见坑：**权限隔离、增量更新、引用准确性**

---

## Week 13 · RAG 工程基础 + agentic RAG 心智

### 任务 1：经典 RAG vs Agentic RAG（1.5 小时）

- **类型**：核心阅读
- **资源**（中英文混合，按这个顺序读）：
  - LangChain 官方《Conceptual guide: Retrieval》：https://python.langchain.com/docs/concepts/retrieval/
  - Anthropic《Contextual Retrieval》（必读，2024 年提出，至今仍是 RAG 工程基线）：https://www.anthropic.com/news/contextual-retrieval
  - 中文（宝玉翻译，质量过关）：https://baoyu.io/translations/anthropic/introducing-contextual-retrieval
  - LangChain《What is an agentic RAG》：https://blog.langchain.dev/agentic-rag-with-langgraph/
- **做什么**：抓 4 个核心区分
  1. **经典 RAG**：固定管线 = retrieve → augment → generate
  2. **Agentic RAG**：retrieval 是 agent 的工具，agent 决定**查不查、查什么、什么时候停**
  3. **Contextual Retrieval**（Anthropic 提出）：用 LLM 给每个 chunk 加上下文摘要再 embed，retrieval 失败率降低 49%
  4. **Multi-step retrieval / Self-RAG**：检索后自我评估，不满意再检
- **产出**：`notes/phase-3.5/rag-concepts.md` 第一节 `## 四种 RAG 范式对比`

---

### 任务 2：chunking 与 embedding 的工程坑（1 小时）

- **类型**：阅读
- **资源**：
  - Chroma 团队《Evaluating Chunking Strategies for Retrieval》：https://research.trychroma.com/evaluating-chunking
  - LangChain《Text splitters》：https://python.langchain.com/docs/concepts/text_splitters/
  - Pinecone《Chunking Strategies》：https://www.pinecone.io/learn/chunking-strategies/
- **做什么**：抓 3 个工程结论
  1. 没有"通用最优"的 chunk size，但 **256–1024 tokens + 10–20% overlap** 是一个能打的 baseline
  2. **语义切分** > 固定大小切分，但实现成本高，企业场景常用"按文档结构（章节/段落）切" + "按 token 切"组合
  3. **embedding 模型选择比 chunk size 重要**：2026 年 baseline = OpenAI text-embedding-3-large / Cohere embed-v4 / BGE-M3
- **产出**：在 `rag-concepts.md` 加一节 `## chunking 决策清单`（≤10 行的 checklist）

---

### 任务 3：多源检索与重排序（1.5 小时）

- **类型**：阅读 + 小动手
- **资源**：
  - LangChain《Hybrid Search》：https://python.langchain.com/docs/integrations/retrievers/
  - Cohere Rerank 文档：https://docs.cohere.com/docs/overview-of-rerank
  - Anthropic Contextual Retrieval 那篇博客的"Rerank 章节"
  - 中文（沧海九粟，验证过质量合格）：https://www.bilibili.com/video/BV1QY411k7Wc/ （混合检索原理）
- **做什么**：
  1. 理解 **稠密检索（向量）+ 稀疏检索（BM25）** 为什么经常一起用
  2. 理解 **重排序**（rerank）为什么是低成本高收益的一步——通常 top-50 → top-5 重排能把质量再拉一档
  3. **小动手**：在本地起一个最小的"BM25 + 向量"混合检索 demo（任选一个 TS 库，如 `@langchain/community/retrievers/ensemble`），用 5 篇 markdown 文档跑通
- **产出**：
  - `rag-concepts.md` 加一节 `## 混合检索 + Rerank：什么时候值得加`
  - `notes/phase-3.5/hybrid-retrieval-hello/`：可跑的最小 demo

---

### 任务 4：中文视频补直觉（1 小时）

- **类型**：视频（中文）
- **资源**（任选一条，**不要全看**）：
  - 沧海九粟《LangChain RAG 完整实战》：https://www.bilibili.com/video/BV1Bx4y1d7vp/
  - Datawhale《动手学 RAG》（公众号置顶或 GitHub 搜 `learn-rag`）
- **做什么**：1.5x 过一遍，**只补直觉，不照抄代码**（视频里大概率是 Python，你 Phase 3.5 全部用 TS）
- **产出**：无

---

### 任务 5：企业 RAG 的三个真坑（1 小时）

- **类型**：阅读 + 思考
- **资源**：
  - LangChain《Multi-tenant RAG》：https://python.langchain.com/docs/how_to/multi_vector/ （含权限隔离）
  - Pinecone《Production RAG》：https://www.pinecone.io/learn/series/rag/
  - Anthropic Cookbook 中的 contextual retrieval notebook：https://github.com/anthropics/anthropic-cookbook/tree/main/skills/contextual-embeddings
- **做什么**：在 `rag-concepts.md` 末尾写一节 `## 企业 RAG 的三个真坑`，针对每条 ≥3 行：
  1. **权限隔离**：metadata filtering / per-tenant index / row-level security 三种方案的取舍
  2. **增量更新**：文档变了怎么办？全量重建 vs 增量 upsert vs 软删除
  3. **引用准确性**：让 LLM 输出 citation 是一回事，让 citation **真的指向支撑该结论的 chunk** 是另一回事
- **产出**：`rag-concepts.md` 完整定稿

---

## Week 14 · Agentic RAG 实战 + 评估

### 任务 6：决定技术栈（0.5 小时）

- **类型**：决策
- **做什么**：从下面三个 TS 路线**选一个**做 Week 14 的核心项目：
  - **路线 A（推荐）**：LangChain.js + LangGraph.js — 生态最全、agentic 模式最成熟。https://js.langchain.com/docs/tutorials/rag/
  - **路线 B**：Mastra RAG — 你 Phase 3 已经熟，少切换成本。https://mastra.ai/docs/rag/overview
  - **路线 C（更接近 mini-agent 哲学）**：原生 Anthropic SDK + 自己写检索循环 — 最少抽象、最大掌控
- **产出**：在 `notes/phase-3.5/enterprise-rag-agent/README.md` 写一段"我选 X 的理由"

---

### 任务 7：搭 agentic RAG agent v1（3 小时）

- **类型**：核心编码
- **做什么**：搭一个能跑的 agentic RAG agent，规模要求"小而完整"：
  - **数据**：取 5–20 篇真实文档（你自己工作中的、或 Anthropic 官方文档全套、或某个开源项目的 README/docs）
  - **必须包含的 4 个能力**：
    1. **chunking + embedding + 入库**（Chroma / Qdrant / pgvector，本地起）
    2. **混合检索**（向量 + BM25，可用 LangChain `EnsembleRetriever` 或自写）
    3. **Agentic 决策**：agent 拿到用户问题后**自己决定**：直接答 / 检索一次 / 检索多轮 / 拒答
    4. **带 citation 的回答**：每条结论标注来源 chunk id + 文档名
  - **可选加分**：rerank 步骤（用 Cohere Rerank API 或 `@xenova/transformers` 跑本地小型 cross-encoder）
- **产出**：`notes/phase-3.5/enterprise-rag-agent/`，能跑、有 README

---

### 任务 8：RAG 评估理论（1 小时）

- **类型**：阅读
- **资源**（**只读概念，不装框架**）：
  - RAGAS 文档（**只看 Concepts/Metrics 一页**理解指标定义，**不装、不用**）：https://docs.ragas.io/en/stable/concepts/metrics/
  - LangSmith RAG eval 教程：https://docs.langchain.com/langsmith/evaluate-rag
  - Anthropic Cookbook RAG eval notebook（含可直接照抄的 LLM-as-judge prompt）：https://github.com/anthropics/anthropic-cookbook/tree/main/skills/retrieval_augmented_generation
- **做什么**：抓 3 个核心指标的定义（任务 9 你要自己用 TS 实现前两个）
  1. **Faithfulness**：答案是否完全由检索到的 context 支撑（防幻觉）—— 核心计算思路：拆 claim → 逐条 LLM 判定支撑性 → 取均值
  2. **Answer Relevancy**：答案是否切题 —— 核心计算思路：从 answer 反向生成 question → 与原 question 算 embedding 相似度
  3. **Context Precision / Recall**：检索质量本身好不好（任务 9 不强制实现，但要理解）
- **产出**：在 `enterprise-rag-agent/README.md` 加一节 `## 我用什么指标评估这个 agent`，写明每个指标自写 TS 实现的伪代码

---

### 任务 9：写 ≥10 条评估数据 + 跑评估（2.5 小时）

- **类型**：核心编码 + 数据工作
- **做什么**：
  1. **手写 ≥10 个问答对**（基于你 Week 14 用的那批文档），每条包含：
     - `question`
     - `ground_truth_answer`（你认为的标准答案）
     - `ground_truth_chunk_ids`（应该被检索到的 chunk）
     - `category`：`single-hop` / `multi-hop` / `ambiguous` / `should-refuse`（agent 应该拒答的）
  2. **自写 grader**（TS）：实现两个核心指标的 LLM-as-judge（EDD 思路，Phase 4 会更系统学到）：
     - **Faithfulness grader**：把 `answer` 拆成 claims，逐条问 LLM "这个 claim 是否能由提供的 context 支撑"，输出 0/1
     - **Answer Relevancy grader**：让 LLM 反向生成"基于这个 answer 最可能的 question"，与原 question 算 embedding 相似度
     - 两个 grader 都用一个**比被评估 agent 更强的模型**做 judge（如被评估用 Haiku，judge 用 Sonnet）
     - **不引入 RAGAS / Python**：自写 < 200 行 TS 就能起步，可控可调试，比 spawn Python 子进程清爽得多
  3. 跑 3 次（pass^3 思路），记录每条数据的得分
- **产出**：
  - `notes/phase-3.5/rag-evals/dataset.json`
  - `notes/phase-3.5/rag-evals/run-results.md`：每条数据的 3 次得分 + 整体平均
  - **失败案例分析**：至少分析 2 条没过的，写明是检索失败 / 重排失败 / 生成幻觉 / 还是 ground truth 写错了

---

### 任务 10：把 RAG 接成 mini-agent-v2 的工具（可选, 1.5 小时）

- **类型**：整合
- **做什么**：如果时间允许，把 Week 14 搭的 enterprise-rag-agent 包装成 mini-agent-v2 的一个新工具 `search_kb(query)`，让 mini-agent 在 coding 任务中也能查"项目知识库"
- **产出**：`mini-agent-v2` 增加 `search_kb` 工具，README 更新

---

## 可选补充（时间富裕再做）

- **LlamaIndex** 文档（Python，但 agentic RAG 概念讲得最清）：https://docs.llamaindex.ai/en/stable/understanding/agent/agentic_rag/
- **Cohere RAG 课程**：https://docs.cohere.com/page/rag-with-cohere
- **Late Chunking**（Jina AI 提出的新策略）：https://jina.ai/news/late-chunking-in-long-context-embedding-models/
- **GraphRAG**（微软研究院，知识图谱 + RAG）：https://github.com/microsoft/graphrag — 知道有这东西即可，复杂度高，企业落地少
- 沧海九粟《Agentic RAG 实战》（如有更新版本，2026 年）

---

## 明确**不要**做的

- **不要从零造向量库**：用 Chroma / Qdrant / pgvector，不要自己写 ANN 索引
- **不要追新 embedding 模型**：每月都有"最强 embedding"出来，对学习目标无意义。基线选 OpenAI text-embedding-3-large 或 BGE-M3 即可
- **不要陷入"哪个向量库最快"的对比**：企业 RAG 瓶颈极少在向量库性能上
- **不要相信"我的 RAG 准确率 95%"这种营销文**：没有评估方案的"准确率"是耍流氓
- **不要读 CSDN 上的 "RAG 实战 100 例"**：99% 是洗稿或代码片段拼接

---

## 阶段产出物清单

- [ ] `notes/phase-3.5/rag-concepts.md`：四种 RAG 范式 / chunking 决策 / 混合检索 / 企业 RAG 三大坑
- [ ] `notes/phase-3.5/hybrid-retrieval-hello/`：BM25 + 向量混合检索最小 demo（可跑）
- [ ] `notes/phase-3.5/enterprise-rag-agent/`：完整的 agentic RAG agent（TS，可跑，带 citation）
- [ ] `notes/phase-3.5/rag-evals/dataset.json`：≥10 条评估数据
- [ ] `notes/phase-3.5/rag-evals/run-results.md`：3 次评估结果 + 失败分析

---

## 自检清单（进入 Phase 4 前必须全过）

- [ ] 能在白板上画出"经典 RAG"和"agentic RAG"的两张数据流图，并指出 3 处差异
- [ ] 能说出 Anthropic Contextual Retrieval 把 retrieval 失败率降低的核心机制
- [ ] 知道在 chunk size、embedding 模型、检索策略、重排序、prompt 拼接 5 个环节，自己的 agent 出问题最可能在哪个环节
- [ ] 你写的 ≥10 条评估题里，至少有 1 条 `should-refuse`，且 agent 真的拒答了
- [ ] 能向同事 60 秒讲清"agentic RAG 不只是 retrieval + generation 加个 agent 包装"

---

## 过渡到 Phase 4

Phase 4 是生产化阶段——给你前面所有 agent（mini-agent-v2、Hermes MVP、enterprise-rag-agent）补上 evals / observability / sandboxing。Phase 3.5 写的 RAG eval 套件，会在 Phase 4 的 Langfuse / LangSmith 集成里自然复用。

进入 [`phase-4.md`](./phase-4.md)。
