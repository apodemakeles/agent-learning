# Phase 3 · 通用 Agent + 框架对比 + Hermes 精读（Week 9–13）

> 目标：从 coding 场景抽身，理解 Mastra / deepagentsjs / Stagehand 等 TS 原生 agent 框架；精读 Hermes Agent 源码与设计思想；动手写一个简化版 Hermes SKILL 闭环。

---

## 本阶段定位（30 秒读完）

Phase 2 教你理解 coding agent，Phase 3 教你通用 agent。重点项目：
- **deepagents / deepagentsjs**（LangChain）：把 Claude Code 四要素抽象成框架
- **Mastra**：TS 原生 agent 框架（Agents/Tools/Workflows/Memory 四件套）
- **Stagehand**：TS 浏览器 agent 黄金样本
- **Hermes Agent**：闭环学习系统的代表作（但只读官方源，剔除中文洗稿）

**Hermes 的特殊处理**：你前面已经确认，中文圈围绕它的"完整指南 / 深度解读"矩阵大多是 LLM 洗稿。本阶段 Hermes 相关只用**官方 GitHub README + 官方文档 + 源码**，中文博客一律不读。

---

## 前置条件与时间预算

- **前置**：Phase 2 自检全过；`mini-agent-v2` 能用
- **时间**：Week 9–13，每周 5–8h（Week 13 是 mini-week 只 5h），合计 ≈ 35h
- **交付**：
  - `notes/phase-3/research-agent-mastra/`（Mastra 版研究 agent）
  - `notes/phase-3/research-agent-deepagents/`（deepagentsjs 版）
  - `notes/phase-3/hermes-skill-mvp/`（你自己的 Hermes SKILL 闭环原型）
  - `notes/phase-3/framework-choice.md`（框架选型决策树）
  - `notes/phase-3/protocols-comparison.md`（MCP / ACP / A2A 三协议横向对比 + ACP demo）

---

## 学习目标

1. 能用 Mastra、deepagentsjs 两个框架各写一个能跑的 agent
2. 能用决策树解释"什么场景选 Mastra、什么场景选 LangGraph-js、什么场景自己手搓"
3. 能读懂 Hermes 的五层记忆架构，并实现一个简化版
4. 理解 Stagehand 的 `act/extract/observe` 三原语为什么是浏览器 agent 的突破
5. 能说出 MCP、ACP、A2A 三个 agent 协议各自解决什么问题、谁是 tool 层谁是 agent 层、当前标准化进展到哪

---

## Week 9 · 框架选型建立心智

### 任务 1：Harrison Chase《How to think about agent frameworks》（1 小时）

- **类型**：阅读
- **资源**：https://blog.langchain.com/how-to-think-about-agent-frameworks/
- **做什么**：建立 "framework vs runtime vs harness" 三层分层心智
- **产出**：`framework-choice.md` 开一节 `## Chase 三层分层`

---

### 任务 2：Harrison Chase《Your harness, your memory》（1 小时）

- **类型**：阅读
- **资源**：https://www.langchain.com/blog/your-harness-your-memory
- **做什么**：理解 2026 年"harness = memory"的新范式
- **产出**：`framework-choice.md` 加一节

---

### 任务 3：两篇对比博客精读（2 小时）

- **类型**：阅读
- **资源**：
  - https://www.chanl.ai/blog/ai-agent-frameworks-compared-2026-what-ships （生产视角）
  - https://www.speakeasy.com/blog/ai-agent-framework-comparison （Mastra / Vercel AI SDK 讲得比较到位）
- **做什么**：边读边填 `framework-choice.md`，列出 9 个框架的**特点 + 适用场景 + 坑**
- **产出**：`framework-choice.md` 第一版决策表

---

### 任务 4：中文视频补直觉（1 小时）

- **类型**：视频（一定中文）
- **资源**：
  - 沧海九粟《Deep Agents 实战》https://www.bilibili.com/video/BV1CPXpBYEui/
  - 唐国梁 Tommy《2026 AI Agent 框架终极指南》https://www.bilibili.com/video/BV1SqAVzLEci/
- **做什么**：1.5x 过一遍
- **产出**：无

---

### 任务 5：写决策树（2 小时）

- **类型**：总结
- **做什么**：画一张"框架选型决策树"，输入：业务场景（coding / 研究 / 客服 / 浏览器 / RAG）+ 技术栈偏好 + 团队规模。输出：推荐的框架 + 理由
- **产出**：`framework-choice.md` 定稿（含决策树）

---

## Week 10 · deepagents 精读与 TS 实战

### 任务 6：deepagents 原博 + 仓库（2 小时）

- **类型**：阅读 + 源码
- **资源**：
  - 原博：https://blog.langchain.com/deep-agents/
  - Python 主仓：https://github.com/langchain-ai/deepagents
  - **TS 版仓（重点）**：https://github.com/langchain-ai/deepagentsjs
  - 官方 overview：https://docs.langchain.com/oss/python/deepagents/overview
- **做什么**：
  1. 读 Harrison 原博，抓四要素：System Prompt / Planning / Sub Agents / File System
  2. 对比 Python 版和 TS 版的 API 差异（**注意 TS 版可能落后 Py 版 1–2 个 minor version**）
- **产出**：`notes/phase-3/deepagents-notes.md`

---

### 任务 7：官方教程 Deep Research Agent（3 小时）

- **类型**：跟教程动手
- **资源**：
  - 官方：https://docs.langchain.com/oss/python/deepagents/deep-research （Python 示例）
  - 配合 deepagentsjs 仓库里的示例代码改成 TS
- **做什么**：用 deepagentsjs 搭一个"给定主题 → 产出 5 页行业综述"的 research agent
- **产出**：`notes/phase-3/research-agent-deepagents/`（可跑的 TS 项目）

---

### 任务 8：deep-agents-from-scratch（2 小时）

- **类型**：阅读 Notebook
- **资源**：https://github.com/langchain-ai/deep-agents-from-scratch
- **做什么**：
  - Python Notebook，5 个进阶 demo，从 ReAct 到完整 research agent
  - **不重写**，只看思路
- **产出**：在 `deepagents-notes.md` 加一节 `## deep-agents-from-scratch 关键思路`

---

## Week 11 · Mastra + Stagehand

### 任务 9：Mastra 官方 90 分钟课（2 小时）

- **类型**：官方教程
- **资源**：
  - https://mastra.ai/learn （免费的 "Build Your First Agent in TypeScript"）
  - 中文实操（知乎，谨慎对待但这条是验证过的真实教程）：https://zhuanlan.zhihu.com/p/1951326837922836531
- **做什么**：完整跟一遍，理解 Mastra 的 Agent/Tool/Workflow/Memory 四件套
- **产出**：`notes/phase-3/mastra-hello/`（跟完官方课的代码）

---

### 任务 10：Mastra 复刻 research agent（3 小时）

- **类型**：核心编码
- **做什么**：用 Mastra 实现和任务 7 相同的 research agent（主题 → 5 页综述），以便能横向对比
- **产出**：`notes/phase-3/research-agent-mastra/`

---

### 任务 11：对比两个 research agent（1 小时）

- **类型**：总结
- **做什么**：
  - 对比代码行数、依赖体积、首 token 延迟、总耗时
  - 写一份 ≤1000 字的 DIFF 笔记（`framework-choice.md` 加一节）
  - 重点：抽象粒度差异（Mastra 的三件套 vs deepagentsjs 的 `createDeepAgent`）
- **产出**：`framework-choice.md` 补一节 `## 我的实测：Mastra vs deepagentsjs`

---

### 任务 12：Stagehand 浏览器 agent（2 小时）

- **类型**：阅读 + 动手
- **资源**：
  - 仓库：https://github.com/browserbase/stagehand
  - 文档：https://docs.stagehand.dev/v3/basics/agent
  - 对比：https://scrapfly.io/blog/posts/stagehand-vs-browser-use
- **做什么**：
  1. 读文档，理解 `act/extract/observe/agent` 四原语
  2. 装 Stagehand，跑一个 hello world（"打开 Wikipedia 搜 AI agent 并返回首段"）
- **产出**：`notes/phase-3/stagehand-hello/`

---

## Week 12 · Hermes Agent 精读 + SKILL MVP

### 任务 13：Hermes 官方（3 小时）

- **类型**：阅读 + 源码
- **资源**（**只看官方源**）：
  - GitHub：https://github.com/NousResearch/hermes-agent
  - 官方文档：https://hermes-agent.nousresearch.com/
  - Nous Research releases：https://nousresearch.com/releases/
  - Awesome（仅作为 reference 查 skills 样例）：https://github.com/0xNyk/awesome-hermes-agent
- **做什么**：
  1. 完整读 README + 所有 docs（官方的）
  2. 重点看：
     - SKILL.md 闭环学习机制
     - 五层记忆（短期 context / 向量 / 全文搜索 / SKILL.md / Honcho 用户画像）
     - SQLite + FTS5 存储设计
  3. 画出 Hermes 架构图
- **产出**：`notes/phase-3/hermes-architecture.md`（自己画的架构图）

**明确不读**：知乎/腾讯云上的"Hermes 全面调研解读 / 万字深度拆解 / 完整指南"全部跳过。

---

### 任务 14：Hermes SKILL 闭环 TS MVP（4 小时，跨 2 天）

- **类型**：核心编码
- **做什么**：用 TS + `better-sqlite3`（开启 FTS5）实现 Hermes 闭环最小子集：
  1. Agent 主循环：模型 + ≥3 工具（其中一个是"查过去的 SKILL"）
  2. **SKILL.md 自动创建**：一次任务超过 N 次工具调用或用户显式触发时，调 LLM 压缩轨迹写盘
  3. **SKILL 索引注入**：启动扫盘，把所有 SKILL 的 `name + trigger` 注入 system prompt，完整内容通过 `skill_view(name)` 工具读
  4. **FTS5 会话检索工具** `session_search(query)`
  5. **简化版 Honcho** `user_profile.md`：每轮结束调 LLM "当前画像 + 新事实 → 新画像"
- **产出**：`notes/phase-3/hermes-skill-mvp/`

---

### 任务 15：3 次任务实验（2 小时）

- **类型**：端到端实验
- **做什么**：
  - 连续 3 次任务，任务间有明显"可复用流程"关联（如：查天气 → 查天气 + 穿衣建议 → 查天气 + 穿衣建议 + 日程提醒）
  - 第 3 次 prompt 长度应**明显短于**第 1 次，且产出质量不低
  - 把结果记录到 `hermes-skill-mvp/README.md` 的"实验结果"一节
- **产出**：实验记录 + GAP 文档（"我的实现 vs Hermes 真实架构"）

---

### 任务 16：B 站 Hermes 中文入门视频（0.5 小时，可选）

- **类型**：视频
- **资源**：01Coder《【手把手教程】Hermes Agent 接入飞书》 https://www.bilibili.com/video/BV1DMDnBNEXj/
- **做什么**：看完一遍，对照你的 MVP 有什么差距
- **产出**：无

**其他 Hermes B 站视频（"20 万字深入解析"等）一律不看**——都是 AI 合成播报营销号。

---

## Week 13 · 协议与互操作性（mini-week, 5h）

> 这周不写大代码、不读大论文。目的只有一个：**建立 agent 通信协议层的坐标系**，知道 2026 年地图上有哪几面旗、各自插在哪。
>
> 为什么单独留一周：这一层 2025–2026 还在标准战阶段，不学的代价不是"现在做不出 agent"，而是**6 个月后看到 ACP / A2A / Microsoft Agent Framework 的争论时完全跟不上**。

### 任务 17：MCP 复盘（0.5 小时）

- **类型**：复盘
- **做什么**：你前面已经在 Phase 2（hello-claw / Claude Code 插件里打包 MCP server）和 Phase 4（MCP 安全）多次接触 MCP，这里只做一件事——**用一句话写下"MCP 是什么、解决什么、不解决什么"**
- **产出**：`notes/phase-3/protocols-comparison.md` 第一节 `## MCP（tool 层）`
- **关键认知**：MCP 是 **agent ↔ tool / data source** 的标准化，**不是** agent ↔ agent

---

### 任务 18：ACP 官方文档（1.5 小时）

- **类型**：核心阅读
- **资源**（**只看一手源**）：
  - 官方站点：https://agentcommunicationprotocol.dev/
  - GitHub 主仓：https://github.com/i-am-bee/acp
  - BeeAI 平台（ACP 主要推动者，IBM Research 孵化）：https://beeai.dev/
  - 规范文档（spec）：在官方站点的 `Specification` 标签下
- **做什么**：
  1. 读官方 "Why ACP / What is ACP" 一章
  2. 抓三个核心概念：**Agent Manifest**（agent 自描述）、**Run / Message** 抽象、**Stateful vs Stateless** 模式
  3. 弄清 ACP 与 MCP 的关系：**ACP 复用 MCP 的传输层（JSON-RPC over HTTP/SSE/stdio），但定义 agent ↔ agent 语义**
- **产出**：`protocols-comparison.md` 第二节 `## ACP（agent 层 / IBM 系）`

---

### 任务 19：A2A 官方文档（1 小时）

- **类型**：核心阅读
- **资源**（**只看一手源**）：
  - 官方站点：https://a2aproject.github.io/A2A/
  - GitHub 主仓：https://github.com/a2aproject/A2A
  - Google Cloud 公告：https://cloud.google.com/blog/products/ai-machine-learning/a2a-a-new-era-of-agent-interoperability
- **做什么**：
  1. 读 "Concepts" 一节
  2. 抓三个核心概念：**Agent Card**（A2A 版的 manifest）、**Task / Message / Artifact**、**Push Notifications**
  3. 弄清 A2A 与 ACP 的差异：**A2A 由 Google + Linux Foundation 推动，长任务异步通信是核心；ACP 更轻、和 MCP 复用栈**
- **产出**：`protocols-comparison.md` 第三节 `## A2A（agent 层 / Google 系）`

---

### 任务 20：协议横向对比 + 最小 demo（2 小时）

- **类型**：动手 + 总结
- **做什么**：
  1. **对比表**（写到 `protocols-comparison.md`）：
     - 维度：层次 / 推动方 / 传输 / 状态模型 / 生态成熟度 / 核心抽象 / 我会在什么场景下用
     - 行：MCP / ACP / A2A / 自己手搓 HTTP
  2. **最小 demo**（二选一，重点是亲手跑过一次，**不追求完整功能**）：
     - **A 选项（推荐）**：clone `i-am-bee/acp`，跑通其中 `examples/` 下任意一个 TS 示例（一个最小的 ACP server + client 互发消息）
     - **B 选项**：clone `a2aproject/a2a-samples`（https://github.com/a2aproject/a2a-samples），跑通其中一个 hello-world
  3. 把跑通的 demo 放到 `notes/phase-3/protocols-demo/`，README 里写：
     - 启动命令
     - 一次完整 message 的请求/响应 JSON 截图
     - 你观察到的"和 MCP 最大的不同点"3 条
- **产出**：
  - `notes/phase-3/protocols-comparison.md` 完整对比表 + 决策建议
  - `notes/phase-3/protocols-demo/` 一个能跑的最小 demo

---

### 任务 21（可选）：Microsoft Agent Framework / AutoGen 合并背景（0.5 小时）

- **类型**：阅读
- **资源**：
  - https://learn.microsoft.com/en-us/agent-framework/overview/agent-framework-overview
  - 官方公告（AutoGen → MAF）：https://devblogs.microsoft.com/foundry/introducing-microsoft-agent-framework/
- **做什么**：知道微软 2025 把 AutoGen 合并进 Microsoft Agent Framework，并且 MAF 同时**支持 MCP + A2A**——这是大厂对协议层的最新表态
- **产出**：在 `protocols-comparison.md` 末尾加一行 `## 大厂动向：MAF 同时押注 MCP + A2A`

---

### 不要做的事

- **不要试图实现自己的协议**：这一周的目的是建立坐标系，不是发明轮子
- **不要陷入"哪个协议会赢"的争论**：当前没人知道，2026 中前看更清楚
- **不要读中文"ACP/A2A 万字深度解读"**：这一层中文资料更稀薄、洗稿比例更高，**直接读官方英文文档**

---

## 可选补充（时间富裕再做）

- Vercel AI SDK agent 能力：https://strapi.io/blog/langchain-vs-vercel-ai-sdk-vs-openai-sdk-comparison-guide
- AWS Strands Agents（TS + Py SDK）：https://strandsagents.com/
- 深度研究 agent 架构对比（腾讯云，警惕标题党但内容基本对）：https://cloud.tencent.com/developer/article/2615247
- 三大行业案例：https://zhuanlan.zhihu.com/p/15446999399 （客服/销售/营销 agent 真实落地）
- 沧海九粟《3.5h LangGraph 官方课程中文讲解》 https://www.bilibili.com/video/BV1AY4aeRETY/

## 明确**不要**读的

- 任何中文"Hermes Agent 万字深度解读 / 完整指南 / 从入门到精通"（全部是洗稿）
- B 站《开源神器！Hermes 智能体 20 万字深入解析》（营销号）
- 任何标题含"2026 最强 / 一期讲透 / 彻底吃透"的
- AutoGen 相关内容（**已进入维护模式**，微软已合并进 Microsoft Agent Framework）
- 2024 上半年的框架对比文章（已经严重过时）

---

## 阶段产出物清单

- [ ] `notes/phase-3/research-agent-mastra/`：Mastra 版 research agent，能跑
- [ ] `notes/phase-3/research-agent-deepagents/`：deepagentsjs 版，能跑
- [ ] `notes/phase-3/stagehand-hello/`：Stagehand hello world
- [ ] `notes/phase-3/hermes-skill-mvp/`：简化版 Hermes SKILL 闭环，3 次实验有记录
- [ ] `notes/phase-3/framework-choice.md`：框架选型决策树 + Mastra vs deepagentsjs 实测对比
- [ ] `notes/phase-3/hermes-architecture.md`：你画的 Hermes 架构图
- [ ] `notes/phase-3/protocols-comparison.md`：MCP / ACP / A2A 横向对比 + 决策建议
- [ ] `notes/phase-3/protocols-demo/`：一个能跑的最小 ACP 或 A2A demo

---

## 自检清单（进入 Phase 3.5 前必须全过）

- [ ] 能说出 Mastra 的 Agent/Tool/Workflow/Memory 四件套分别解决什么
- [ ] 能说出 deepagentsjs 的 `createDeepAgent` 底层在做什么
- [ ] 能说出 Stagehand 的 `act/extract/observe` 相对于 Puppeteer/Playwright 的增量价值
- [ ] 你的 Hermes SKILL MVP 第 3 次任务效率**可证明**地优于第 1 次
- [ ] 能在"coding / research / browser / customer-service"四个场景下各推荐一个合适框架并给出理由
- [ ] 能在 30 秒内向同事解释 MCP 和 ACP/A2A 不在同一层、各自解决什么

---

## 过渡到 Phase 3.5

Phase 3.5 是企业 RAG 专题：把"agent 怎么和企业知识库结合"系统学一遍，是做通用业务 agent 绕不开的一环。

进入 [`phase-3.5.md`](./phase-3.5.md)。
