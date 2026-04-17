# Phase 2 · Coding Agent 深水区（Week 4–7）

> 目标：理解 Cline、opencode、Claude Code 这些"真家伙"在架构上比你的 mini-agent 多做了什么；把 hello-claw 跑完；最终把你的 mini-agent 升级到 v2（支持 subagent + `/compact`）。

---

## 本阶段定位（30 秒读完）

4 周的时间分成 4 条线，每周一条主线：
- **Week 4**：Claude Code 系列（官方 best practices + 源码拆解 + subagent pattern）
- **Week 5**：Cline 源码精读（TS 生态黄金样本）
- **Week 6**：Datawhale `hello-claw` 跟完 + opencode 架构快速过
- **Week 7**：升级你的 mini-agent 到 v2（subagent + `/compact`）

**特别说明**：OpenClaw 项目本身是真实的，但中文圈二手解读文章大多是 LLM 洗稿。本阶段**只用** Datawhale `hello-claw`（真实教程）+ OpenClaw 官方 GitHub README + 少量 B 站动手视频（林亦 LYi / 林粒粒呀）。所有知乎/腾讯云的"OpenClaw 万字深度解读"一律不读。

---

## 前置条件与时间预算

- **前置**：Phase 1 自检全过；`mini-agent-v1` 能跑；已安装并用过 Claude Code / Cline / Cursor 之一
- **时间**：Week 4–7 每周 7–8h，合计 ≈ 30h
- **交付**：
  - `notes/phase-2/mini-agent-v2/`（升级版，支持 subagent + `/compact`）
  - `notes/phase-2/comparison.md`（mini-agent vs OpenClaw vs Cline vs Claude Code 对比）

---

## 学习目标

1. 能画出一个完整 coding agent 的分层架构（从 CLI → Session → Agent Loop → Tools → LLM）
2. 能解释 Claude Code 的 `/compact` 是怎么工作的（至少 5 层细节）
3. 能理解 subagent / spawn 模式的 trade-off（何时用 / 何时不用）
4. 能读懂 TS 写的 agent 源码（Cline / opencode 这种量级）
5. 跑通一个 OpenClaw 实例并写一个自己的 Skill

---

## Week 4 · Claude Code 系列

### 任务 1：Anthropic 官方 Best Practices（1.5 小时）

- **类型**：阅读
- **资源**：
  - 官方 Best Practices：https://code.claude.com/docs/en/best-practices
  - 官方 Sandboxing：https://code.claude.com/docs/en/sandboxing （Phase 4 会再用）
- **做什么**：抓 CLAUDE.md / compact / Plan Mode / Subagent 的官方定义
- **产出**：`notes/phase-2/comparison.md` 先开好空结构，填入 Claude Code 官方视角的 4 个概念定义

---

### 任务 2：宝玉译《使用 Claude Code：会话管理与 100 万上下文》（0.5 小时）

- **类型**：阅读
- **资源**：https://baoyu.io/translations/claude-code-session-management
- **做什么**：Session / compact / clear / subagent 四件套的中文实战翻译
- **产出**：无

---

### 任务 3：精读 Claude Code 逆向仓库（3 小时）

- **类型**：源码阅读
- **资源**：
  - `injekt/claude-code-reverse` https://github.com/injekt/claude-code-reverse
  - 重点文件：`docs/02-agent-loop.md`（或类似 agent loop 文档）
- **做什么**：
  1. clone 下来，**重点看 docs/ 目录里的分析文档**（不要硬读混淆代码）
  2. 找到 agent loop 的伪代码（一般用 `async function*` + `yield`）
  3. 对比你自己的 mini-agent，差在哪几层
- **产出**：
  - `comparison.md` 加一节 `## Claude Code Agent Loop`，画出它的状态机

---

### 任务 4：中文源码视频 + 另一个逆向仓库（2 小时）

- **类型**：视频 + 代码
- **资源**：
  - B 站唐国梁 Tommy《Claude Code 源码曝光 硬核拆解：1884 文件》https://www.bilibili.com/video/BV1zR9JBREua/
  - B 站 Koala 聊开源《这样逆向分析 Claude Code》https://www.bilibili.com/video/BV1MJ8fzzEcw/
  - 参考仓库：`ThreeFish-AI/analysis_claude_code` https://github.com/ThreeFish-AI/analysis_claude_code
- **做什么**：
  - 视频用 1.5x 过，重点记"他们如何逆向、发现了什么模式"
  - 参考仓库只作为 reference，不要从头读
- **产出**：
  - `comparison.md` 加一节 `## 逆向发现的 5 个设计模式`（如：Coordinator-Worker / Prompt Cache 分段 / 四层权限等）

---

### 任务 5：Cognition vs Anthropic 对照读（1 小时）

- **类型**：阅读（Phase 0 读过，现在带着实操经验再读）
- **资源**：
  - Anthropic 《How we built multi-agent research system》
  - Cognition 《Don't Build Multi-Agents》
  - ihower 点评（你在 Phase 0 已经读过）
- **做什么**：**Phase 0 读是建立词汇，现在带着 Phase 1 的实操经验再读**——你对"multi-agent 到底值不值得"的判断会比第一次精准得多
- **产出**：
  - `comparison.md` 加一节 `## 我对 multi-agent 的判断（v2）`，对比 Phase 0 的版本

---

## Week 5 · Cline 源码精读

### 任务 6：Cline 仓库初探（1.5 小时）

- **类型**：阅读
- **资源**：
  - Cline 中文 README：https://github.com/cline/cline/blob/main/locales/zh-cn/README.md
  - 阿里云《Cline：29.7K Star 最强开源 AI 编程搭子》https://developer.aliyun.com/article/1652535 （作为快速入门导览，警惕标题党但内容基本对）
- **做什么**：
  1. clone Cline 仓库到本地
  2. 先读中文 README + 配置说明，理解它的**用户视角**
  3. 用 VSCode 打开 Cline 源码目录，熟悉它的 monorepo 结构
- **产出**：无

---

### 任务 7：Cline Agent Loop 精读（3 小时）

- **类型**：源码阅读
- **做什么**：
  1. 找到 Cline 核心 agent loop 所在文件（一般在 `src/core/` 下）
  2. 重点看：
     - Plan mode vs Act mode 切换
     - Shell Integration（如何和 VSCode 终端交互）
     - 无头浏览器工具
     - MCP marketplace 集成
  3. 和你的 mini-agent 对照
- **产出**：
  - `comparison.md` 加一节 `## Cline 架构`，画出分层图
  - 列出 Cline 有、你没有的 5 个能力

---

### 任务 8：claude-code-router 作为"轻量对照"（1 小时）

- **类型**：源码阅读 + 阅读
- **资源**：
  - 仓库：https://github.com/musistudio/claude-code-router
  - 官方中文 README：https://github.com/musistudio/claude-code-router/blob/main/README_zh.md
  - 项目初衷（作者博客）：https://github.com/musistudio/claude-code-router/blob/main/blog/zh/%E9%A1%B9%E7%9B%AE%E5%88%9D%E8%A1%B7%E5%8F%8A%E5%8E%9F%E7%90%86.md
- **做什么**：
  - 这是个"本地 HTTP 代理 + 请求重写 + Transformer"的小 TS 项目
  - 读它的 Express 路由设计和 transformer 插件机制
  - 作为"如何逆向一个商业 agent"的**入门级参考**
- **产出**：无

---

### 任务 9：Aider Repo Map（2 小时，设计思想补课）

- **类型**：阅读
- **资源**：
  - 官方文档：https://aider.chat/docs/repomap.html
  - 官方博客：https://aider.chat/2023/10/22/repomap.html
- **做什么**：
  - Aider 是 Python 的，但它的 "Repo Map"（用 tree-sitter + PageRank 给大型 repo 做摘要）是经典思想
  - 想一下：这个设计如果在 TS 里实现，怎么做？
- **产出**：
  - `comparison.md` 加一节 `## Repo Map 思想：如何给 agent 大仓库上下文`

---

## Week 6 · OpenClaw / hello-claw + opencode

### 任务 10：林亦 LYi / 林粒粒呀视频（1 小时）

- **类型**：视频（建立直觉）
- **资源**：
  - 林亦 LYi《一个视频搞懂 OpenClaw！》https://www.bilibili.com/video/BV1jEAaz3E6K/
  - 林粒粒呀《OpenClaw 小龙虾保姆级安装教程》https://www.bilibili.com/video/BV1UsP1zqEWc/
- **做什么**：看 OpenClaw 是什么、能干什么、安装流程大概是啥
- **产出**：无

---

### 任务 11：OpenClaw 官方安装 + 最小可用（2 小时）

- **类型**：动手
- **资源**：
  - OpenClaw 官方 GitHub：https://github.com/openclaw/openclaw
  - 官方文档（以 GitHub 上的为准，勿用任何中文"保姆级"文章）
- **做什么**：
  1. 安装 OpenClaw（本地跑）
  2. 跑通最小 hello world
  3. 尝试接入一个 Channel（飞书 / Discord / Telegram 任选）
- **产出**：
  - `notes/phase-2/my-claw/` 目录下有你的配置文件（脱敏）
  - `NOTES.md` 记 3 条"装的过程中踩的坑"

---

### 任务 12：跟完 Datawhale `hello-claw`（4 小时）

- **类型**：核心动手教程
- **资源**：
  - 仓库：https://github.com/datawhalechina/hello-claw
  - 在线文档：https://datawhalechina.github.io/hello-claw/
- **做什么**：
  1. 三篇（领养 / 龙虾大学 / 构建）至少跟完"领养"和"构建第 1、3、5 章"
  2. 构建篇第 3、5 章是**理解 Agent Runtime 的关键**，精读
  3. 按教程给你的 claw 写一个自己的 Skill（比如 `summarize-markdown`）
- **产出**：
  - 你自己的 Skill，放 `my-claw/skills/`
  - `NOTES.md` 补一节 `## OpenClaw 的"灵魂三件套" vs Claude Code 的 CLAUDE.md`

---

### 任务 13：opencode 架构快速过（2 小时）

- **类型**：源码速览 + 中文导读
- **资源**：
  - opencode 仓库：https://github.com/sst/opencode
  - 中文导读（腾讯云，注意警惕标题党但内容基本可用）：https://cloud.tencent.com/developer/article/2647646
  - 第三方源码笔记：https://github.com/ZeroZ-lab/learn-opencode
- **做什么**：
  - **不需要精读 opencode**，因为你已经精读了 Cline，opencode 作为"另一个 TS 编码 agent 样本"快速过一遍
  - 重点看它的 Plan/Build 双模式 prompt 设计
- **产出**：
  - `comparison.md` 补一节 `## opencode vs Cline：两种 TS coding agent 路线`

---

## Week 7 · mini-agent-v2（动手）

### 任务 14：加 subagent 能力（4 小时）

- **类型**：核心编码
- **做什么**：
  1. 复制 `mini-agent-v1/` → `mini-agent-v2/`
  2. 实现 `spawn_subagent(task, tools, budget)` 工具：
     - 子 agent 独立上下文窗口
     - 父 agent 只看到子 agent 最终结果
     - 显式传 token budget + tool 白名单
  3. 跑一个测试："给这个目录写 README"，子 agent 并行做 3 个子任务（扫目录 / 读 package / 读核心代码）
- **产出**：
  - `mini-agent-v2/` commit：`feat: add subagent support`

---

### 任务 15：实现简化版 `/compact`（4 小时）

- **类型**：核心编码
- **资源参考**：
  - Anthropic context engineering 博客的"compaction"策略
  - 已读的 Claude Code 逆向资料（Week 4 任务 3–4）
- **做什么**：
  1. 实现三层压缩：
     - **微观**：旧 `tool_result` 替换为占位符
     - **大文件剥离**：摘要前剥离超长 blob
     - **全局摘要**：调用 LLM 生成 9 段式结构化摘要，注入新会话
  2. 暴露 `/compact [focus]` 命令
  3. 在一个长对话（>100k token）里测试压缩比
- **产出**：
  - `mini-agent-v2/` commit：`feat: add /compact command with 3-layer compression`
  - 压缩前/后 token 对比日志存到 `mini-agent-v2/traces/compact-experiment.md`

---

### 任务 16：对比报告（2 小时）

- **类型**：总结
- **做什么**：整理 `comparison.md` 成一份完整对比报告，维度：
  1. Context 管理（mini-agent-v1 / v2 / OpenClaw / Cline / Claude Code / opencode）
  2. Tool 抽象
  3. Sub-agent 支持
  4. Safety & Sandboxing（这部分你在 Phase 2 只是知道概念，Phase 4 会补）
  5. Persistence（session / memory）
- **产出**：`notes/phase-2/comparison.md` 最终版

---

## 可选补充（时间富裕再做）

- B 站陈成（云谦）《打造生产级 Coding Agent：架构、挑战与解决方案》 https://www.bilibili.com/video/BV1KQSYBhEuN/ （蚂蚁 Neovate Code 架构分享，生产视角）
- Boris Cherny（Claude Code 作者）访谈：https://every.to/podcast/how-to-use-claude-code-like-the-people-who-built-it （英文，但作者级细节）
- B 站慢学主义 AI 产品经理《不要构建多 agent 系统：精读 Cognition》https://www.bilibili.com/video/BV1GG2aBtE1t/

## 明确**不要**读的

- 任何知乎/腾讯云/CSDN 上的"OpenClaw 完全指南 3.2W 字"类文章（洗稿矩阵）
- 任何"Hermes Agent 20 万字深度解析"类文章
- B 站 UP 主"酌沧"的视频（疑似 AI 合成播报）
- B 站标题含"最新版 / 吃透 / 少走 99% 弯路"的
- Matt Pocock / 吴恩达 / LangChain 1.0 这几个视频的**中文搬运版**（实际是英文），要看就去 YouTube 看原版（但本路径硬性要求视频中文，所以直接跳过）

---

## 阶段产出物清单

- [ ] `notes/phase-2/mini-agent-v2/`：支持 subagent + `/compact`
- [ ] `notes/phase-2/comparison.md`：6 个项目横向对比（包括你的 v1/v2）
- [ ] `notes/phase-2/my-claw/`：一个跑起来的 OpenClaw 实例 + 你自己写的 1 个 Skill
- [ ] 至少 15 次 git commit（v2 相关）

---

## 自检清单（进入 Phase 3 前必须全过）

- [ ] 能徒手画一个 coding agent 的 5 层架构图
- [ ] 能解释 `/compact` 为什么要分层，每一层解决什么问题
- [ ] 能说出 Cline 和 opencode 各自的**独特设计决策**至少 2 条
- [ ] 能说出"subagent 什么时候值得用、什么时候不值得"
- [ ] 读 Claude Code 逆向文章不再需要频繁查术语
- [ ] 你的 mini-agent-v2 能在 100k+ token 对话里稳定工作

---

## 过渡到 Phase 3

Phase 3 要你从 coding 场景抽身，用 Mastra / deepagentsjs 做通用 agent，并实现一个简化版 Hermes SKILL 闭环。

进入 [`phase-3.md`](./phase-3.md)。
