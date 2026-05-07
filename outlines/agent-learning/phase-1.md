# Phase 1 · 手搓最小 Agent（Week 2–3）

> 目标：用 TypeScript/Node 从零写一个 ≤300 行、真能跑 coding 任务的 mini-agent。跟一篇 TS 博客手敲一遍 + 对照同作者 GitHub 仓库做工程化补强，最后出自己的版本。

---

## 本阶段定位（30 秒读完）

这是整条路径最关键的两周。Phase 0 是理论，Phase 2+ 是看别人怎么做——中间这 2 周是**你自己写一个能跑的**。写完之后，你后面看 Cline / Claude Code 源码才不会是"看不懂的魔法"，而是"哦他这块比我多做了 XX"。

策略：**先跟 Ivan Leo 博客一行行敲，再对照同作者 GitHub 仓库**。Ivan Leo 这篇是 Thorsten Ball 思路在 TS 里的最干净复现：200 行不到、4 个工具一次性给齐，并且用通用 `@anthropic-ai/sdk`——国内第三方 Anthropic 兼容代理通过 `ANTHROPIC_BASE_URL` 一行就能接入，避免 Vertex/Bedrock 那种平台专用 SDK 的坑。博客文字版讲思路，作者同名 GitHub 仓库讲工程化（错误处理、目录组织、tool 抽象）——文字搞明白思路，仓库对照看工程化，是这两周的主线。

---

## 前置条件与时间预算

- **前置**：Phase 0 自检清单全过；有 Anthropic API Key（也可以用 OpenAI，但路径以 Anthropic 为主）；Node ≥ 20
- **时间**：Week 2 ≈ 8h，Week 3 ≈ 7h，合计 15h
- **交付**：`notes/phase-1/mini-agent-v1/`（一个能跑的 TS 项目，git init 管理）

---

## 学习目标

1. 能从零写一个 agent loop，包含：系统提示词、工具定义、tool_use/tool_result 往返、错误恢复
2. 工具至少实现 5 个：`read_file` / `list_files` / `create_file` / `edit_file`（字符串精确替换）/ `bash`（前 4 个跟 Ivan Leo 博客即得，`bash` 在 task 5 自己加）
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

### 任务 3：跟 Ivan Leo 的 TS 教程完整写一遍（5 小时，跨 2 天）

- **类型**：核心编码
- **时间**：5 h
- **资源**：
  - 主：https://ivanleo.com/blog/building-an-agent
  - 辅（仅在卡壳时对照，不要先看）：https://github.com/ivanleomk/building-an-agent
- **为什么换掉原来的 Kevin Yank**：那篇用 `@anthropic-ai/vertex-sdk`（Google Vertex 专用），国内第三方 Anthropic 兼容代理用不了；而且 URL 写"javascript"实际代码全是 TS，新手容易踩坑。Ivan Leo 这篇直接用通用 `@anthropic-ai/sdk`，配 `baseURL` 就能接第三方代理；TS 类型完整、200 行内 4 个工具一次到位。
- **做什么**：
  1. 新建 `notes/phase-1/mini-agent-v1/`，用 `npm init -y` 或 `pnpm init`（也能用 `bun init -y`，文章原例用 Bun，但 Node 20+ 一样能跑，全 phase 后续工具链以 npm/pnpm 为准更稳）
  2. 装依赖：`@anthropic-ai/sdk` `zod` `chalk` `tree-cli`（与博客一致）；如果用 Node 而非 Bun，再加 `tsx`、`dotenv`
  3. **配置环境变量**（**国内代理关键**）：在项目根建 `.env`：
     ```
     ANTHROPIC_API_KEY=sk-xxx
     ANTHROPIC_BASE_URL=https://your-anthropic-compatible-proxy.example.com
     ```
     初始化客户端时显式传 `baseURL`：

     ```ts
     import { Anthropic } from "@anthropic-ai/sdk"
     const client = new Anthropic({
       apiKey: process.env.ANTHROPIC_API_KEY,
       baseURL: process.env.ANTHROPIC_BASE_URL, // 不传时 SDK 默认走 api.anthropic.com
     })
     ```

     博客里写的是 `new Anthropic()`（默认从环境变量读 key 走官方 endpoint），你只要多传一个 `baseURL` 就能切到第三方代理，其他代码完全不用动
  4. 跟博客四个小节 **一行行敲**（不复制粘贴）：
     1. 捕获用户输入 + chalk 着色（`getUserInput` + `run` 主循环）
     2. 接 Claude 单轮对话（`client.messages.create` + 把 user/assistant 推到 `conversations` 数组）
     3. 加 `read_file` 工具：用 zod 定义 schema，`toJSONSchema` 转 JSON Schema 喂给 Claude，处理 `tool_use` / `tool_result` 回路（注意 `processUserInput` 这个布尔标志位的用法，理解它为什么必要）
     4. 把 `list_files / create_file / edit_file` 三个工具补齐
  5. 跑通最终验证：让 agent 读一个文件并总结、让 agent 改写一个本地小 .txt 文件
  6. **关键约束**：不懂任何一段就停下来问 Claude/GPT，不要囫囵抄完；如果博客代码（截至 2025-07 发布）与最新 SDK 类型不符（比如 zod v4 的 `toJSONSchema` 导出位置变化），参考作者 GitHub 仓库当前版本修复
- **产出**：
  - `mini-agent-v1/` 第一版能跑，包含 4 个工具（`read_file / list_files / create_file / edit_file`） + 基本 agent loop
  - 一个 commit：`feat: initial agent following Ivan Leo tutorial`
  - `mini-agent-v1/NOTES.md` 开头记录"用了哪个第三方 Anthropic 兼容代理 + baseURL 形态是什么"（脱敏，别泄露 key 与具体 host）

---

## Week 3 任务清单

### 任务 4：读 ivanleomk 仓库代码与博客对照（3 小时）

- **类型**：代码阅读 + 重构
- **时间**：3 h
- **资源**：
  - 主：https://github.com/ivanleomk/building-an-agent （task 3 已经短暂用过）
  - 速览（不实现）：https://ivanleo.com/blog/migrating-to-react-ink （作者后续文章，看演进方向）
- **背景**：task 3 跟着博客文字版敲完之后，仓库里还藏着一些博客没展开的工程化细节——这一步专门把这些细节挖出来 port 进来。
- **做什么**：
  1. clone 下来逐文件读，重点对比"博客文字版给出的样例代码" vs "仓库实际代码"在以下 4 方面的工程化差异：
     - `Tool` 接口的抽象方式（zod schema 怎么转 JSON Schema、execute 函数签名、async/sync 处理）
     - 工具执行错误如何捕获 + 怎么把错误结构化回填给 agent（博客里只是个 try/catch + `"Error executing tool"`，仓库里通常更细）
     - `tool_use` / `tool_result` 在多轮里的状态管理（博客里的 `processUserInput` 标志位是否还能改进）
     - 目录结构 / 模块拆分（博客里都堆在 `agent.ts` 一个文件里）
  2. 把你觉得仓库版比博客版更好的设计**手动 port 到自己的 mini-agent-v1**——不要直接 clone 替换，要逐处复制并理解
  3. 速览 *Building a Coding CLI with React Ink* 一文，**不实现**，只在笔记里记 1 行"接下来工程化的方向是什么"（task 4 不做，但脑子里有数）
- **产出**：
  - 一个 commit：`refactor: apply improvements from ivanleomk's repo`
  - 在 `mini-agent-v1/NOTES.md` 加一节 `## 博客 vs 仓库的差异`，列至少 3 条具体改进点（每条说明：博客怎么写的 → 仓库怎么写的 → 你为什么决定 port）

---

### 任务 5：补齐 bash 工具 + 强化错误处理（2 小时）

- **类型**：编码
- **时间**：2 h（原计划 3 h；4 个工具中 `read_file / list_files / create_file / edit_file` 已在 task 3 跟博客敲出，task 5 只剩 `bash` + 把博客版工具的错误处理做硬）
- **做什么**：
  1. **新增 `bash(command)` 工具**：
     - 用 `child_process.exec`（promisify 后异步包装，参考 task 3 里 `list_files` 已经在用 `execAsync` 的写法）
     - 加 30s 超时（`{ timeout: 30_000 }`）
     - 合并 stdout + stderr 一起返回（agent 经常需要 stderr 做错误判断）
     - 超时或非零退出码也要把已有输出回填，不要直接抛异常吃掉信息
  2. **强化 `edit_file` 错误信号**：博客版 `edit_file` 在 `old_string` 找不到时会静默 `replace`（结果是文件原样），agent 会以为修改成功——这是个坑。改成：找不到时返回结构化错误字符串，例如：

     ```
     ERROR: old_string not found in <path>.
     Hint: the file currently contains (first 500 chars): <truncated content>
     ```

     这样 agent 看到 tool_result 就知道要先 `read_file` 再重试
  3. **所有工具加返回值长度上限**（建议 8 KB / 8000 字符），超出后追加 `\n... [truncated, total <N> chars]`——避免一次 `read_file` 大文件就把上下文撑爆
  4. **手动测试两轮**：
     1. "在当前目录建一个 hello.txt 写入 hello world"（验 create_file）
     2. "用 bash 跑 ls 并告诉我有几个 .ts 文件"（验 bash + agent 能解析 stdout）
     3. 故意触发 `edit_file` 错误：让 agent 改一段不存在的字符串，观察它能否根据错误信息自动 `read_file` 重试
- **产出**：
  - commit：`feat: add bash tool and harden tool error handling`
  - 在 `mini-agent-v1/NOTES.md` 加一段记录：`edit_file` 改成结构化错误前后 agent 行为差别（特别是它有没有学会"先 read 再 edit"）

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
- [ ] 包含 5 个工具：`read_file / list_files / create_file / edit_file / bash`
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
