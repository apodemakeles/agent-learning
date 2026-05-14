# Phase 0 · 坐标系校准（Week 1）

> 目标：让"agent / workflow / context engineering / multi-agent"这四个词在你脑子里有清晰的行业共识定义，以后读任何文章不再被术语晃晕。

---

## 本阶段定位（30 秒读完）

这一周**没有代码产出**（全路径唯一的纯阅读周）。你会读 Anthropic 的 3 篇基石博客 + Harrison Chase 的两篇 "In the Loop" + 一篇 multi-agent research blog，外加两段中文视频做直觉校准。最终写一页纸笔记，用自己的话定义四个核心术语。

---

## 前置条件与时间预算

- **前置**：你已经用过 Claude Code / Cursor 做过一些日常开发；会调 OpenAI / Anthropic 的 Chat API
- **时间**：6–8 小时
- **节奏建议**：3 天读核心 + 1 天看视频 + 1 天写笔记

---

## 学习目标（读完这周你应该能…）

1. 用一句话区分 "workflow" 和 "agent"，并说出 Anthropic 定义的 5 种 workflow 模式
2. 用一句话解释 "context engineering" 相对于 "prompt engineering" 增加了什么
3. 能判断一个任务"该用 workflow 还是 agent"
4. 理解 Harrison Chase 的"cognitive architecture 频谱"和 Anthropic 二分法为什么不完全一致
5. 能说出"multi-agent 什么时候值得做、什么时候不值得做"的至少 3 条判断依据

---

## Week 1 任务清单

### 任务 1：破冰视频（30 分钟）

- **类型**：视频
- **时间**：0.5 h
- **资源**：[隔壁的程序员老王《原来写一个 AI Agent 这么简单》](https://www.bilibili.com/video/BV1UMVKzEESL/)（9 分钟本体，预留时间倍速 + 暂停思考）
- **做什么**：看完后暂停想一下——他演示的 tool-use 循环，和你自己手写过的循环有什么异同？
- **产出**：无，只是热身

---

### 任务 2：读 Anthropic《Building Effective Agents》（2 小时）

- **类型**：核心阅读
- **时间**：2 h（英文 + 中文对读）
- **资源**：
  - 英文原文：https://www.anthropic.com/engineering/building-effective-agents
  - 中文译本（ArthurChiao，最佳中译）：https://arthurchiao.art/blog/build-effective-ai-agent-zh/
- **做什么**：
  1. 第一遍快速扫英文原文，30 分钟内读完
  2. 第二遍精读中文译本，在纸上/OneNote 画出 5 种 workflow 模式的示意图（prompt chaining / routing / parallelization / orchestrator-workers / evaluator-optimizer）
  3. 记下"workflow vs agent"的官方定义
- **产出**：
  - `notes/phase-0/key-concepts.md` 中开一节 `## 5 种 Workflow 模式`，每种模式用自己的话写 2–3 句话 + 一个你能想到的业务场景例子

---

### 任务 3：读 Anthropic《Effective context engineering for AI agents》（2 小时）

- **类型**：核心阅读
- **时间**：2 h
- **资源**：
  - 英文：https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
  - 中文精读（可选，知乎有反爬不稳定）：https://zhuanlan.zhihu.com/p/1957222140714661519
- **做什么**：
  1. 读英文原文
  2. 重点抓 4 个策略：**just-in-time retrieval** / **compaction** / **sub-agents** / **structured note-taking**
  3. 想一下："prompt engineering"和"context engineering"的边界在哪里？
- **产出**：
  - 在 `key-concepts.md` 加一节 `## Context Engineering`，列出 4 个策略 + 每个策略"解决了什么问题"

---

### 任务 4：读 Anthropic《Writing effective tools for AI agents》（1.5 小时）

- **类型**：核心阅读
- **时间**：1.5 h
- **资源**：https://www.anthropic.com/engineering/writing-tools-for-agents
- **做什么**：
  1. 注意这篇是"用 agent 帮你写 agent 工具"——agentic tool engineering 的 meta 视角
  2. 抓 3 点：工具描述的 token 预算、返回值设计、evaluation-driven 迭代
- **产出**：
  - 在 `key-concepts.md` 加一节 `## 工具设计 5 条原则`

---

### 任务 5：读 Harrison Chase 两篇"In the Loop"博客（1 小时）

- **类型**：对照阅读
- **时间**：1 h
- **资源**：
  - https://blog.langchain.com/what-is-an-agent/
  - https://blog.langchain.com/what-is-a-cognitive-architecture/
- **做什么**：
  - Chase 的核心观点：**agent 不是二分法，而是频谱**（LLM call → chain → router → state machine → autonomous agent）
  - 对比：Anthropic 二分 vs Chase 频谱——哪个描述更接近你手写 agent 时的真实感受？
- **产出**：
  - `key-concepts.md` 加一节 `## Anthropic 二分 vs Chase 频谱：我的判断`，200 字以内

---

### 任务 6：读 Anthropic《How we built our multi-agent research system》（1 小时）

- **类型**：核心阅读
- **时间**：1 h
- **资源**：
  - 英文：https://www.anthropic.com/engineering/multi-agent-research-system
  - 中文译本（ArthurChiao）：https://arthurchiao.art/blog/built-multi-agent-research-system-zh/
- **做什么**：
  1. 先读，再去读 Cognition 反对派的经典文（任务 7，**必须同一天读**）
  2. 抓：orchestrator-worker 架构、15× token 成本、什么任务值得用 multi-agent
- **产出**：暂不写，等任务 7 读完一起写

---

### 任务 7：读 Cognition《Don't Build Multi-Agents》 + ihower 平衡点评（1 小时）

- **类型**：对照阅读（与任务 6 配对）
- **时间**：1 h
- **资源**：
  - 英文原文：https://cognition.ai/blog/dont-build-multi-agents
  - ihower 中文点评（把两派观点整合的最佳文章）：https://ihower.tw/blog/12776-multi-agent-or-single-agent
  - 中文翻译（可选）：https://zhuanlan.zhihu.com/p/1917353362287986623
- **做什么**：
  1. 读 Cognition 原文——**Devin 团队为什么反对 multi-agent？**
  2. 读 ihower——他怎么调和两派？
  3. 想：你自己的业务场景里，multi-agent 值不值得？
- **产出**：
  - `key-concepts.md` 加一节 `## Multi-Agent：何时用、何时不用`
  - 给出至少 3 条判断依据

---

### 任务 8：中文视频建立直觉（1 小时，可选但推荐）

- **类型**：视频
- **时间**：1 h（原视频更长，挑 1 h 核心片段 + 倍速）
- **资源**：马克的技术工作坊《Agent 的概念、原理与构建模式 —— 从零打造一个简化版的 Claude Code》
  - https://www.bilibili.com/video/BV1TSg7zuEqR/
- **做什么**：
  - 用 1.5x 速度跟一遍，重点看他演示 ReAct vs Plan-and-Execute 的代码差异
  - 如果他用 Python 你也不用抄代码，看思路就行
- **产出**：无

---

### 任务 9：一页纸总结（1 小时）

- **类型**：笔记
- **时间**：1 h
- **做什么**：把 `key-concepts.md` 整理成一页纸，包含：
  1. **Agent 定义**（你的版本）
  2. **Workflow vs Agent** 区别
  3. **Context Engineering** 定义 + 4 策略
  4. **Multi-Agent** 判断依据
  5. **5 种 workflow 模式**速查表
- **产出**：`notes/phase-0/key-concepts.md`（最终版，一页纸）

---

## 可选补充（时间富裕再做）

- warm3snow《Anthropic AI Agent 系列导读：15 篇博客带你从入门到精通》— https://www.cnblogs.com/informatics/p/19625887 （把 Anthropic 15 篇博客整理成索引，可以当成后续每周的阅读路径图）
- Anthropic《Managed Agents: Decoupling the brain from the hands》— https://www.anthropic.com/engineering/managed-agents （如果有兴趣做 "agent-as-a-service" 方向）

## 明确**不要**读的（避坑）

- 任何标题含"万字长文 / 保姆级 / 最新版 / 完全指南"的中文 agent 博客
- Lilian Weng 2023 年的综述（术语过老）
- 任何 2023 年及之前的 agent 综述文章

---

## 阶段产出物清单

- [ ] `notes/phase-0/key-concepts.md`，包含 5 节（Agent 定义 / Workflow vs Agent / Context Engineering / Multi-Agent 判断 / 5 种模式速查）
- [ ] 读完 6 篇核心博客（4 篇 Anthropic + 2 篇 Chase + 1 篇 Cognition）
- [ ] 看完至少 1 段中文视频

---

## 自检清单（进入 Phase 1 前必须全过）

- [ ] 能不看笔记说出 5 种 workflow 模式的名字和一句话定义
- [ ] 能判断一个任务"该用 workflow 还是 agent"，并说出理由
- [ ] 能解释 Anthropic 为什么推荐 "compaction" 而不是直接上更大上下文
- [ ] 能说出 Cognition 反对 multi-agent 的**至少 2 条**技术理由
- [ ] 知道 "tool description 的 token 预算"为什么重要

如果 2 条以上答不上，**回去补**，不要进入 Phase 1。

---

## 过渡到 Phase 1

Phase 1 要你用 TypeScript 从零写一个 coding agent。读完 Phase 0 后你应该已经对"这个 agent 设计成什么样合理"有了判断，**不再是照抄教程**。

进入 [`phase-1.md`](./phase-1.md)。
