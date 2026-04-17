# Phase 1 · 手搓最小 Agent（Week 2–3）

> 目标：用 TypeScript/Node 从零写一个 ≤300 行、真能跑 coding 任务的 mini-agent。两本 TS 教程并行跑，最后出自己的版本。

---

## 本阶段定位（30 秒读完）

这是整条路径最关键的两周。Phase 0 是理论，Phase 2+ 是看别人怎么做——中间这 2 周是**你自己写一个能跑的**。写完之后，你后面看 Cline / Claude Code 源码才不会是"看不懂的魔法"，而是"哦他这块比我多做了 XX"。

策略：**先跟 Kevin Yank，再对照 ivanleomk**。原因是 Kevin Yank 的博客文章结构像 Thorsten Ball 那种"从 0 一步步长出来"，最适合入门；ivanleomk 是工程化完整版，用来看"从手搓到 Claude Code 式结构"的桥梁。

---

## 前置条件与时间预算

- **前置**：Phase 0 自检清单全过；有 Anthropic API Key（也可以用 OpenAI，但路径以 Anthropic 为主）；Node ≥ 20
- **时间**：Week 2 ≈ 8h，Week 3 ≈ 7h，合计 15h
- **交付**：`notes/phase-1/mini-agent-v1/`（一个能跑的 TS 项目，git init 管理）

---

## 学习目标

1. 能从零写一个 agent loop，包含：系统提示词、工具定义、tool_use/tool_result 往返、错误恢复
2. 工具至少实现 4 个：`read_file` / `write_file` / `edit_file`（diff 方式）/ `bash`
3. 能跑完一个小 refactor 任务（比如"把这个 JS 文件改成 TS 并加类型"）
4. 理解"为什么 mini-swe-agent 100 行就能在 SWE-bench 拿 74%"

---

## Week 2 任务清单

### 任务 1：读 Thorsten Ball 原文（2 小时）

- **类型**：阅读
- **时间**：2 h
- **资源**：https://ampcode.com/how-to-build-an-agent
- **做什么**：
  1. 原文用 Go 写，你**不需要抄代码**，只看思路
  2. 抓核心一句话：**"An agent is an LLM, a loop, and enough tokens"**
  3. 注意他的 tool 接口设计（name + description + input_schema + handler）
- **产出**：无，进入任务 2

---

### 任务 2：读 Tony Bai 中文复现文（1 小时）

- **类型**：阅读
- **时间**：1 h
- **资源**：https://tonybai.com/2025/04/18/reproduce-thorsten-balls-code-agent/
- **做什么**：
  - Tony Bai 是 Go 圈资深博主，这篇是 Thorsten Ball 的深度中文复现 + 补充思考
  - 重点看他对"代码 agent 没有护城河"这个命题的论证
- **产出**：无

---

### 任务 3：跟 Kevin Yank 的 TS 教程完整写一遍（5 小时，跨 2 天）

- **类型**：核心编码
- **时间**：5 h
- **资源**：https://kevinyank.com/posts/how-to-build-an-agent-in-javascript/
- **做什么**：
  1. 新建 `notes/phase-1/mini-agent-v1/`，`pnpm init` / `npm init`
  2. 装依赖：`@anthropic-ai/sdk`、`zod`、`tsx`
  3. **完整抄一遍代码**——不是复制粘贴，而是跟着文章一行一行敲
  4. 敲完后跑通：让 agent 读一个文件并总结
  5. **关键**：理解每一段 code 在做什么，不懂就停下来问 Claude/GPT
- **产出**：
  - `mini-agent-v1/` 第一版能跑，包含：
    - `read_file` 工具
    - `list_files` 工具
    - 基本 agent loop
  - 一个 commit：`feat: initial agent following Kevin Yank tutorial`

---

## Week 3 任务清单

### 任务 4：读 ivanleomk 的代码并对照改进（3 小时）

- **类型**：代码阅读 + 重构
- **时间**：3 h
- **资源**：https://github.com/ivanleomk/building-an-agent
- **做什么**：
  1. clone 下来看他的目录结构和分支
  2. 重点对比：他怎么抽象 `Tool`？错误怎么处理？tool 结果怎么传回？
  3. 把你觉得他比 Kevin Yank 版更好的地方**手动 port 到你的 mini-agent-v1**
- **产出**：
  - 一个 commit：`refactor: apply improvements from ivanleomk's approach`
  - 在 `mini-agent-v1/NOTES.md` 里记下"我参考了他的 XX 设计因为 XXX"

---

### 任务 5：加 4 个核心工具（3 小时）

- **类型**：编码
- **时间**：3 h
- **做什么**：在 `mini-agent-v1` 里实现：
  1. `read_file(path)` — 已有
  2. `write_file(path, content)` — 新增
  3. `edit_file(path, old, new)` — 新增，基于字符串精确替换
  4. `bash(command)` — 新增，用 `child_process.execSync` 或 `execa`
- **注意事项**：
  - `edit_file` 要在 old 找不到时返回明确错误，让 agent 能自我纠正
  - `bash` 必须加超时（30s）
  - 所有工具返回必须是字符串，超长要截断
- **产出**：
  - commit：`feat: add write/edit/bash tools`
  - 手动测试：让 agent"在当前目录建一个 hello.txt 写入 hello world"

---

### 任务 6：跑一个真实小任务（2 小时）

- **类型**：端到端验证
- **时间**：2 h
- **做什么**：
  1. 准备一个小型 JS 文件（50–100 行，有 3–4 个函数）
  2. 让你的 mini-agent 执行任务："把这个 JS 文件改成 TS 并加类型标注，运行 tsc 确认无错"
  3. 观察：它能不能成功？失败在哪一步？
- **产出**：
  - 把完整 transcript 保存到 `mini-agent-v1/traces/first-refactor.md`
  - 在 `NOTES.md` 记 5 条"它做得好的 / 做得差的"观察

---

### 任务 7：读 mini-swe-agent 对照（1 小时）

- **类型**：代码阅读
- **时间**：1 h
- **资源**：https://github.com/SWE-agent/mini-swe-agent
- **做什么**：
  1. 读它的 `__init__.py` 或核心文件（总共才 ~100 行 Python）
  2. 观察：它**只有一个 bash 工具**，凭什么在 SWE-bench 拿 74%？
  3. 读配套教程 https://minimal-agent.com/
- **产出**：
  - 在 `NOTES.md` 写一段 200 字：**"mini-swe-agent 给我的启发"**，讨论"工具数 vs 能力"的 trade-off

---

### 任务 8：Phase 1 收尾博客（补读，1 小时）

- **类型**：阅读
- **时间**：1 h
- **资源**：Anthropic《Writing effective tools for AI agents》https://www.anthropic.com/engineering/writing-tools-for-agents
- **做什么**：Phase 0 已经读过一遍，但那时你还没写过真实工具。现在再读一遍，**你会发现很多细节之前没注意**（tool description 该多长、错误消息该怎么写等）
- **产出**：
  - 在 `mini-agent-v1/NOTES.md` 新开一节 `## 再读 Writing effective tools 的新感受`

---

## 可选补充（时间富裕再做）

- Mark Cianfrani《Building an AI Agent with TypeScript》https://cianfrani.dev/posts/building-an-ai-agent-with-typescript/ — 带"本地 LLM（LM Studio）"分支
- 马克的技术工作坊《Claude Code 从 0 到 1 全攻略》B 站 https://www.bilibili.com/video/BV14rzQB9EJj/ — 看完再回头看你的 mini-agent 缺什么

## 明确**不要**做的

- 不要上来用 LangChain / LangGraph / Vercel AI SDK 之类的框架——Phase 1 的价值就是手搓
- 不要加 "subagent / context compaction / memory"——那是 Phase 2 的事
- 不要追求代码优雅——能跑、能自证理解就够了

---

## 阶段产出物清单

- [ ] `notes/phase-1/mini-agent-v1/` 是一个可运行的 TS 项目
- [ ] 包含 4 个工具：`read_file / write_file / edit_file / bash`
- [ ] 能跑通"把 JS 改成 TS"任务，有 transcript 存档
- [ ] `NOTES.md` 至少 3 节：参考来源、真实任务观察、mini-swe-agent 启发
- [ ] 至少 5 次 git commit，能看出演进过程

---

## 自检清单（进入 Phase 2 前必须全过）

- [ ] 合上所有资料，你能 10 分钟内从零写出 agent loop 骨架（伪代码也行）
- [ ] 能解释为什么 `edit_file` 返回错误让 agent 自我纠正比"agent 直接重写整个文件"好
- [ ] 能说出"工具描述写法"影响 agent 行为的至少 3 个方面
- [ ] 你的 mini-agent 至少跑挂过 3 次，你知道每次为什么挂
- [ ] 看到 `tool_use` / `tool_result` 这种字段不会发懵

---

## 过渡到 Phase 2

Phase 2 要你读 Cline / opencode / Claude Code 源码，跑 Datawhale `hello-claw`，并把你的 mini-agent 升级到支持 subagent 和 `/compact`。

进入 [`phase-2.md`](./phase-2.md)。
