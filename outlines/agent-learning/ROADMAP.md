# AI Agent 开发学习路径（17–19 周）

> 适用对象：已经自己手写过 ReAct 风格 tool-use 循环，想从 demo 走向**能设计 + 手搓 + 生产化** agent 的开发者。
> 语言偏好：**文字中文优先（高质量英文可用），视频必须中文。**
> 技术栈偏好：**TypeScript / Node.js**（涉及 Python 的部分只阅读不重写）。

---

## 0. 这份路径是什么 / 不是什么

**是**：
- 一份按"周 → 任务 → 产出"粒度写好的执行手册，每一步告诉你读什么、看什么、动手写什么、交付什么
- 资料经过两轮 subagent 搜索和验证，官方源优先、中文洗稿已剔除
- 强调"每阶段除 Phase 0 外都要动手写代码"

**不是**：
- 一份"大而全的资料索引"，只收"能直接上手"的核心资料
- 一份"包教包会"的讲义——这份路径**假设你会自己读论文和英文博客**，它的价值在于**筛选 + 排序 + 动手题**，而不是代替你学

---

## 1. 时间盘算

- **总周数**：17–19 周（4–4.5 个月）
- **每周预算**：6–8 小时（±2 小时弹性，Week 12 是 mini-week 只 5h）
- **总投入**：≈ 120–145 小时
- **关键原则**：**每阶段必须有一次"能跑的代码产出"**，没跑起来就别进入下一阶段

### 周级主干进度表

| 周 | 阶段 | 主题 | 关键产出 |
|---|---|---|---|
| **Week 1** | Phase 0 | 坐标系校准（理论） | 一页纸笔记：定义 agent / workflow / context engineering / multi-agent |
| **Week 2–3** | Phase 1 | 手搓 mini-agent（TS/Node） | `mini-agent-v1`：≤300 行 TS，支持 read/write/edit/bash 四工具 |
| **Week 4–7** | Phase 2 | Coding Agent 深水区 | `mini-agent-v2`：加 subagent + `/compact` 压缩；一份对比报告 |
| **Week 8–11** | Phase 3 | 通用 agent + 框架对比 | 两个迷你 research agent（Mastra 版 + deepagentsjs 版）+ Hermes SKILL 原型 |
| **Week 12** | Phase 3 (mini) | 协议与互操作性 | MCP / ACP / A2A 横向对比 + 一个最小 ACP 或 A2A demo |
| **Week 13–14** | Phase 3.5 | 企业 RAG + agentic retrieval | `enterprise-rag-agent`（TS）+ ≥10 条评估数据 + 失败分析 |
| **Week 15–17** | Phase 4 | 生产级实践 + 前沿 | mini-agent 接上 Langfuse + Evals + 沙箱；5 个失败样本分析 |
| **Week 18–19** | 弹性/收尾 | 复盘 + 选一个方向深挖 | 你自己的综合项目（选题见 Phase 4 末尾） |

---

## 2. 学习哲学（这份路径的取舍原则）

1. **一手源优先**：Anthropic、LangChain、Cognition、Simon Willison、Chroma 这些原厂/大牛博客 > 中文翻译 > 中文洗稿。中文翻译里，**ArthurChiao、宝玉、ihower、Tony Bai** 四家基本可以闭眼信。
2. **OpenClaw / Hermes 的特殊处理**：项目本身真实（Peter Steinberger、Nous Research），但中文圈涌现的"万字深度解读""保姆级完全指南"几乎全是 LLM 洗稿。**只用官方 README / 官方文档 / Datawhale `hello-claw` / 源码**，中文二手分析文章一概不读。
3. **视频是辅助不是主食**：B 站中文视频用于"建立直觉"和"跟完一遍实操"，不是用来替代读官方文档。严禁靠视频代替阅读。
4. **不做"资料收集瘾"**：每阶段只给 5–10 份核心资料 + 2–4 份可选补充，读完就进下一阶段，不要囤资料。
5. **动手 > 理解 > 记忆**：每阶段的"代码产出"是检验唯一标准，不是看懂多少文章。

---

## 3. 各阶段一句话概要

- **Phase 0（Week 1）**：弄清楚"agent / workflow / context engineering / multi-agent"这四个词的行业共识，不再凭感觉用词
- **Phase 1（Week 2–3）**：用 TS 从零写一个 <300 行能真跑小 refactor 任务的 coding agent
- **Phase 2（Week 4–7）**：读懂 Cline / opencode / Claude Code 比 mini-agent 多做了什么，把 subagent 和 `/compact` 接上
- **Phase 3（Week 8–12）**：从 coding 场景抽身，理解 Mastra / deepagentsjs / Stagehand，用 Hermes 的思想实现一个简化 SKILL 闭环；末尾用一个 mini-week 建立 MCP / ACP / A2A 协议层的坐标系
- **Phase 3.5（Week 13–14）**：把"agent + 企业知识库"系统学一遍，搭一个有评估指标的 agentic RAG（不是经典"检索 + 生成"管线）
- **Phase 4（Week 15–17）**：给你的 agent 接上 evals / observability / 沙箱，并跟上 context rot、prompt injection 这些 2025–2026 前沿

---

## 4. 贯穿始终的习惯（从 Week 1 就开始）

### 4.1 每周固定 2 小时读 feed

订阅下面这几家，每周挑 2–3 篇精读（放到 `notes/feed-log.md` 里记一行）：

- [Anthropic Engineering](https://www.anthropic.com/engineering) — 每 1–2 周必看
- [Cognition Blog](https://cognition.ai/blog/1) — Devin 团队
- [LangChain Blog](https://www.langchain.com/blog)
- [Simon Willison's Weblog](https://simonwillison.net/) — 订阅 RSS，每天刷
- [Hugging Face Daily Papers](https://huggingface.co/papers) — 每周看一次头部 3 篇

中文方面订阅：
- **机器之心**（公众号）— 覆盖广编辑把关严
- **Datawhale**（公众号 / GitHub）— 翻译 + 开源课程
- **Jimmy Song 博客**（jimmysong.io）— 云原生 AI 观测视角
- **宝玉翻译**（baoyu.io）— 高质量 AI 内容翻译

**避雷**：标题里含"保姆级 / 最新版 / 全网最细 / 一文搞懂 / 万字长文 / 存下吧 / 少走弯路"的，默认不读。

### 4.2 每周写 1 份 150 字总结

每周五在 `notes/weekly-YYYY-WW.md` 写一份：

- 本周学了什么（3 条）
- 本周卡在哪（1 条）
- 下周优先做什么（3 条）

写不出来说明这周没学透。

### 4.3 把 agent 真的用起来

- 从 Week 1 开始用 Claude Code / Cursor / Cline 做日常开发（用户已经在用 Cursor）
- 读任何文章都先问自己"如果我是这个 agent 的设计者，我会怎么做？"
- **日常使用 ≠ 学完 agent**，但不日常使用就会学得很空

---

## 5. 仓库组织

```
outlines/agent-learning/
├── ROADMAP.md          ← 本文件（总纲）
├── phase-0.md          ← Week 1 执行手册
├── phase-1.md          ← Week 2–3 执行手册
├── phase-2.md          ← Week 4–7 执行手册
├── phase-3.md          ← Week 8–12 执行手册（含 Week 12 协议 mini-week）
├── phase-3.5.md        ← Week 13–14 执行手册（企业 RAG 专题）
└── phase-4.md          ← Week 15–17 执行手册

notes/                  ← 你的学习笔记（自己创建）
├── phase-0/
│   └── key-concepts.md  ← Phase 0 的一页纸产出
├── phase-1/
│   └── mini-agent-v1/   ← Phase 1 的代码产出（git 仓库）
├── phase-2/
│   ├── comparison.md    ← Cline / opencode / Claude Code 对比
│   └── mini-agent-v2/
├── phase-3/
│   ├── research-agent-mastra/
│   ├── research-agent-deepagents/
│   ├── hermes-skill-mvp/
│   ├── protocols-comparison.md  ← MCP / ACP / A2A 横向对比
│   └── protocols-demo/          ← ACP 或 A2A 最小 demo
├── phase-3.5/
│   ├── rag-concepts.md
│   ├── hybrid-retrieval-hello/
│   ├── enterprise-rag-agent/
│   └── rag-evals/
├── phase-4/
│   ├── evals/
│   ├── observability/
│   └── sandbox/
├── feed-log.md          ← 每周订阅精读记录
└── weekly-YYYY-WW.md    ← 每周总结
```

---

## 6. 如何使用这份路径

1. **严格按 phase 顺序走**，别跳。Phase 0 的理论是后面所有阶段的共同词汇。
2. **每个 phase 文件**打开后，按"Week X 任务清单"逐条打勾。
3. **每个任务都标注了预计时间**，如果你花的时间明显超出 2 倍，停下来想：是资料太难，还是你方向偏了？
4. **产出物清单**是进入下一阶段的门槛，未完成不要强行推进。
5. **自检清单**用来确认你是否真的吃透了（而不是看完了）。如果过不了自检，回去补。
6. **弹性机制**：每个 phase 都有一项"可选补充"，时间紧就跳，时间富裕就读。

---

## 7. 开始

进入 [`phase-0.md`](./phase-0.md) 开始 Week 1。
