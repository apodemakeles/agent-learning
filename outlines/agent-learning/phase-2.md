# Phase 2 · Coding Agent 深水区（Week 4–8）

> 目标：吃透 Claude Code 的全部扩展点（subagent / skill / hook / plugin / plan mode 等）+ 一份完整逆向；把 Datawhale `hello-claw`（OpenClaw 教程）跟到构建篇尾声；最终把你的 mini-agent 升级到 v2（支持 subagent + `/compact`）。

---

## 本阶段定位（30 秒读完）

5 周的时间分成两条主线：

- **Claude Code 主线**（Week 4–6）：从官方 best practices + 逆向，到 subagent / skills / hooks / plugins 四个扩展点全过一遍，再回头精读 prompt cache 分段、四层权限、context 管理等内部机制。
- **OpenClaw 主线**（Week 7）：跟完 Datawhale `hello-claw` 构建篇（第 1–6 章 + 第 8 章），动手写自己的 Skill，接入 ≥2 个 Channel，并写一张和 Claude Code 的横向对照表。
- **落地周**（Week 8）：把前面学到的两条主线收敛到 `mini-agent-v2`——加 subagent（参考 Claude Code 官方 API 形状）+ `/compact` 压缩。

**为什么这样改**：原 Week 5 Cline 精读 + 任务 13 opencode 速览已被砍掉——Cline 当前生态热度大幅下滑，opencode 也不在本阶段主线上；腾出的 ~9.5h 全部投到 Claude Code 进阶（subagent / skill / hook / plugin）和 OpenClaw 深做。这两块比"再看一个 TS coding agent 仓库"对你 Week 8 落地和 Phase 3 Hermes SKILL MVP 都更有直接价值。

**OpenClaw 资料规则保留**：OpenClaw 项目本身真实，但中文圈二手解读文章大多是 LLM 洗稿。本阶段**只用** Datawhale `hello-claw`（真实教程）+ OpenClaw 官方 GitHub + 少量 B 站动手视频（林亦 LYi / 林粒粒呀）。所有知乎/腾讯云的"OpenClaw 万字深度解读"一律不读。

---

## 前置条件与时间预算

- **前置**：Phase 1 自检全过；`mini-agent-v1` 能跑；已安装并用过 Claude Code（本阶段动手任务多次依赖本地 Claude Code）
- **时间**：Week 4–8 每周 6.5–10h，合计 ≈ 38h
- **交付**：
  - `notes/phase-2/mini-agent-v2/`（升级版，支持 subagent + `/compact`）
  - `notes/phase-2/comparison.md`（mini-agent v1/v2 vs OpenClaw vs Claude Code 四方对比）
  - `notes/phase-2/my-cc-skills/`、`notes/phase-2/my-cc-hooks/`、`notes/phase-2/my-cc-plugin/`（动手产物）
  - `notes/phase-2/my-claw/`（OpenClaw 实例 + 你自己的 ≥2 个 Skill）

---

## 学习目标

1. 能画出一个完整 coding agent 的分层架构（从 CLI → Session → Agent Loop → Tools → LLM）
2. 能解释 Claude Code 的 `/compact` 是怎么工作的（至少 5 层细节）
3. 能解释 Claude Code 的 **subagent / skill / hook / plugin** 四种扩展点各自解决什么 + 不能相互替代的原因
4. 能说出 Claude Code prompt cache 分段策略 / 四层权限模型 / 工具白名单与传递 / context window 软硬上限的工作原理（每条 5–10 行）
5. 跑通一个 OpenClaw 实例（≥2 个 Channel），写 ≥2 个 Skill，并能把 OpenClaw 的 SOUL / USER / AGENTS / TOOLS 多文件 prompt 系统和 Claude Code 的 CLAUDE.md + Skills + Subagent 做横向对照

---

## Week 4 · Claude Code 基础与逆向

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
  - `comparison.md` 加一节 `## 逆向发现的 5 个设计模式`（如：Coordinator-Worker / Prompt Cache 分段 / 四层权限等）——本节会在 Week 5 任务 9 和 Week 6 任务 13 进一步深化

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

## Week 5 · Claude Code 进阶 I：Subagent + Skills

> 这一周的目的：把"Claude Code 怎么扩展自己"的两块核心能力——**subagent 和 skill**——吃透。它们会在 Week 8 mini-agent-v2 的 subagent 实现里直接被引用，也是 Phase 3 Hermes SKILL MVP 的预备。

### 任务 6：Subagent 官方文档（1.5 小时）

- **类型**：阅读
- **资源**：
  - Subagents in the SDK：https://code.claude.com/docs/en/agent-sdk/subagents
  - Extend Claude Code 总览（先扫一眼定位）：https://code.claude.com/docs/en/features-overview
- **做什么**：
  1. 抓 subagent 的三件套：**上下文隔离（独立 conversation）/ 并行调度 / 工具白名单 + 专属 system prompt**
  2. 弄清 subagent 的两种定义方式：编程式（Agent SDK）vs 文件式（`.claude/agents/*.md`）vs 内置 `general-purpose`
  3. 记下：父 agent 看到的只是子 agent 的最终结果，中间步骤不进父上下文
- **产出**：
  - `comparison.md` 加一节 `## Claude Code Subagent 的官方 API 形状`，列出关键参数（task description / system prompt / tool whitelist / model / output style 等）

---

### 任务 7：Skills 官方文档 + `anthropics/skills` 仓库（2 小时）

- **类型**：阅读 + 源码
- **资源**：
  - Extend Claude with skills：https://docs.anthropic.com/en/docs/claude-code/skills
  - Anthropic 博客《Introducing Agent Skills》：https://www.anthropic.com/index/skills
  - Anthropic 工程博客《Equipping agents for the real world with Agent Skills》：https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills
  - 官方仓库：https://github.com/anthropics/skills
  - Agent Skills 开放标准：https://agentskills.io
- **做什么**：
  1. `SKILL.md` 三件套：**YAML frontmatter（name / description / 触发条件）+ markdown 正文 + 同目录关联资源（脚本 / 模板 / 数据）**
  2. 在 `anthropics/skills` 里挑 2 个仔细读：一个偏文档处理（如 `document-skills`）+ 一个偏开发工具（如 `example-skills` 下任一）
  3. 弄清 Skills 和原来 `.claude/commands/` slash command 的关系（commands 已并入 skills，但旧的依然兼容）
  4. 记下 Agent Skills 是个**开放标准**，理论上跨工具可移植
- **产出**：
  - `comparison.md` 加一节 `## Claude Code Skill 的官方结构`，包含一个最小 `SKILL.md` 的字段示例

---

### 任务 8：动手 — 装一个 marketplace skill + 自己写一个（2 小时）

- **类型**：核心动手
- **做什么**：
  1. 在本地 Claude Code 跑：
     ```
     /plugin marketplace add anthropics/skills
     ```
     装一个示例 skill set（如 `document-skills`），跑一个 `/skill-name` 验证
  2. 仿照官方仓库结构，自己写一个 skill `summarize-md-folder`：
     - YAML frontmatter：name / description / 何时触发
     - 正文：给 Claude 的指令（输入：一个目录 / 输出：500 字以内的目录综述）
     - 关联资源：可选放一个 prompt 模板文件
  3. 放到 `~/.claude/skills/summarize-md-folder/SKILL.md`（或当前项目的 `.claude/skills/`），实测能被 `/summarize-md-folder` 触发
- **产出**：
  - `notes/phase-2/my-cc-skills/summarize-md-folder/`（你的 skill 目录，git 跟踪）
  - `notes/phase-2/my-cc-skills/NOTES.md` 记 3 条"写 skill 时踩的坑"（比如 description 写法、何时被自动触发 vs 显式 `/...`）

---

### 任务 9：Subagent vs Skill 抽象对比 + 设计模式深读（1.5 小时）

- **类型**：总结
- **做什么**：
  1. 在 `comparison.md` 写一节 `## Subagent 和 Skill 是不是同一个抽象的两面`，重点讨论：
     - 都是"专家模式 + 可触发 + 可携带工具/上下文"
     - 区别：subagent 是**运行时上下文隔离的子进程**，skill 是**指令级 + 资源级的复用单元**
     - 何时该用哪个（短任务 + 知识复用 → skill；长任务 + 工具受控 + 上下文隔离 → subagent）
  2. 把 Week 4 任务 4 列出的"5 个设计模式"展开，每一条加一段**伪代码层级**说明（Coordinator-Worker 怎么调度 / 工具白名单怎么传递 / context isolation 怎么实现）
- **产出**：
  - `comparison.md` 这一节定稿，是 Week 8 任务 18 写 `spawn_subagent` 时的直接依据

---

## Week 6 · Claude Code 进阶 II：Hooks / Plugins / 扩展点地图 + 深入逆向

### 任务 10：Hooks 官方 reference + 写一个 PreToolUse hook（1.5 小时）

- **类型**：阅读 + 动手
- **资源**：https://code.claude.com/docs/en/hooks
- **做什么**：
  1. 区分三档生命周期：
     - **一次/每会话**：`SessionStart` / `SessionEnd`
     - **每轮**：`UserPromptSubmit` / `Stop` / `StopFailure`
     - **每次工具调用**：`PreToolUse` / `PostToolUse`
  2. 写一个最小 `PreToolUse` hook：当工具是 `bash` 且命令里出现 `rm -rf /` 或 `:(){ :|:& };:` 等危险模式时直接 deny
  3. 在本地 Claude Code 实测被拦截
- **产出**：
  - `notes/phase-2/my-cc-hooks/block-dangerous-bash.sh`（或对应 JSON 配置）
  - `notes/phase-2/my-cc-hooks/NOTES.md` 记一段"hook 与 sandbox 的关系"（你会发现 hook 是用户态防御，sandbox 才是系统态防御——Phase 4 会再深化）

---

### 任务 11：Plugins reference + Agent SDK Plugins + 打包（2 小时）

- **类型**：阅读 + 动手
- **资源**：
  - Plugins reference：https://code.claude.com/docs/en/plugins-reference
  - Plugins in the SDK：https://code.claude.com/docs/en/agent-sdk/plugins
- **做什么**：
  1. 弄清 plugin 是 skills + agents + hooks + MCP server **的容器**（一个 manifest + 一组目录）
  2. 把任务 8 的 `summarize-md-folder` skill + 任务 10 的 `block-dangerous-bash` hook 合并打包成一个 plugin `my-cc-toolbox`
  3. 通过 `/plugin install ./my-cc-toolbox`（或当前官方支持的本地安装方式）装上，并在 system init 消息里看到它出现
- **产出**：
  - `notes/phase-2/my-cc-plugin/my-cc-toolbox/`（manifest + skills/ + hooks/ 子目录）
  - `comparison.md` 加一段：plugin 之于 skill/hook，**等价于 monorepo 之于单包**

---

### 任务 12：Plan Mode / Output Styles / Status Line / Slash Commands（1.5 小时）

- **类型**：阅读 + 把扩展点拼成一张图
- **资源**：
  - Extend Claude Code 总览：https://code.claude.com/docs/en/features-overview
  - 各 sub-page 由总览页跳过去（plan mode / output styles / status line / slash commands / settings 等）
- **做什么**：
  1. 把上述四个扩展点各扫一遍，重点记**它们各自插在 agent 生命周期的哪一层**：
     - Plan Mode → agent loop 的总开关（先想后做）
     - Output Style → 最终消息层
     - Status Line → 终端 UI 层
     - Slash Commands → 用户输入层（现已并入 skills）
  2. 不需要动手做完整 demo，**目标是建立一张完整的"扩展点 vs agent loop 位置"映射图**
- **产出**：
  - `comparison.md` 加一节 `## Claude Code 的扩展点地图（subagent / skill / hook / plugin / plan mode / output style / status line / slash command）`，含一张 ASCII / mermaid 流程图

---

### 任务 13：深入逆向 2 — 4 个内部机制（1.5 小时）

- **类型**：源码阅读 + 总结
- **资源**：Week 4 任务 3/4 已经 clone 的 `injekt/claude-code-reverse`、`ThreeFish-AI/analysis_claude_code`
- **做什么**：在 Week 4 任务 4 列出的"5 个设计模式"之上，专门精读 **4 个内部机制**，每个写 5–10 行总结：
  1. **Prompt cache 分段策略**：哪些片段进 cache、cache breakpoint 怎么放、cache miss 时的代价
  2. **四层权限模型**：read / write / execute / network 的权限怎么划分、用户怎么在 settings 里配
  3. **工具白名单与传递**：父 agent 怎么把 tools 子集传给 subagent（与 Week 5 任务 9 对接）
  4. **Context window 软硬上限**：什么时候触发 `/compact`、什么时候硬截断、硬截断时怎么选保留的消息（与 Week 8 任务 19 对接）
- **产出**：
  - `comparison.md` 这一节定稿，是 Week 8 任务 19 实现 `/compact` 的直接依据

---

## Week 7 · OpenClaw / hello-claw 深做 + 对照

### 任务 14：林亦 LYi / 林粒粒呀视频（1 小时）

- **类型**：视频（建立直觉）
- **资源**：
  - 林亦 LYi《一个视频搞懂 OpenClaw！》https://www.bilibili.com/video/BV1jEAaz3E6K/
  - 林粒粒呀《OpenClaw 小龙虾保姆级安装教程》https://www.bilibili.com/video/BV1UsP1zqEWc/
- **做什么**：看 OpenClaw 是什么、能干什么、安装流程大概是啥
- **产出**：无

---

### 任务 15：OpenClaw 官方安装 + 接入 ≥2 个 Channel（2 小时）

- **类型**：动手
- **资源**：
  - OpenClaw 官方 GitHub：https://github.com/openclaw/openclaw
  - 官方文档（以 GitHub 上的为准，勿用任何中文"保姆级"文章）
- **做什么**：
  1. 安装 OpenClaw（本地跑）
  2. 跑通最小 hello world
  3. **接入至少 2 个 Channel**（飞书 + Discord / Telegram 任选其二），目的是亲眼看到 OpenClaw 的"统一网关"是怎么把不同 Channel 的输入统一成 agent 输入的
- **产出**：
  - `notes/phase-2/my-claw/` 目录下有你的配置文件（脱敏）
  - `NOTES.md` 记 3 条"装的过程中踩的坑" + 1 条"≥2 个 Channel 共享同一个 agent 时观察到的有意思现象"

---

### 任务 16：跟完 Datawhale `hello-claw` 构建篇（3 小时）

- **类型**：核心动手教程
- **资源**：
  - 仓库：https://github.com/datawhalechina/hello-claw
  - 在线文档：https://datawhalechina.github.io/hello-claw/
  - 构建篇入口：https://datawhalechina.github.io/hello-claw/cn/build/
- **做什么**：
  1. **领养篇 + 龙虾大学**先快速过完（已经在任务 14/15 装过，主要是把概念词捋顺）
  2. **构建篇精读 + 动手**：第 1–6 章 + 第 8 章
     - 第 1 章 架构哲学：Agent Runtime 和 Chatbot 的本质区别
     - 第 2 章 ReAct 循环：和你 mini-agent-v1 的 loop 对照
     - 第 3 章 提示词系统：**SOUL.md / USER.md / AGENTS.md / TOOLS.md** 多文件分工——和 Claude Code 的 CLAUDE.md + Skills 形成强对照
     - 第 4 章 工具系统：read / write / edit / exec 四工具——和 mini-agent-v1 的 5 工具对照
     - 第 5 章 消息循环与事件驱动：理解"心跳"
     - 第 6 章 统一网关：印证任务 15 的多 Channel 实测
     - 第 8 章 轻量化方案：NanoClaw / Nanobot / ZeroClaw——给"做 agent 是不是非得这么重"一个反例
  3. 第 7 章按个人时间决定（非主线）
- **产出**：
  - `NOTES.md` 加一节 `## 构建篇关键学到的 7 件事`，按章一条

---

### 任务 17：写 ≥2 个 OpenClaw Skill + 写 Claude Code 对照表（2 小时）

- **类型**：核心动手 + 总结
- **做什么**：
  1. 仿任务 8 的 Claude Code `summarize-md-folder` 思路，给你的 OpenClaw 写 ≥2 个 Skill：
     - Skill A：**和 Claude Code 同名同功能**（`summarize-md-folder`），方便对照
     - Skill B：贴合 OpenClaw 的 Channel 场景（比如 `daily-digest`：每天定时把某个 Channel 24h 内的消息汇成一段摘要）
  2. 在 `comparison.md` 加这张表 **`## OpenClaw 灵魂三件套 (SOUL/USER/AGENTS/TOOLS) vs Claude Code (CLAUDE.md + Skills + Subagent + Hooks + Plugins)`**——这一节是 Phase 2 输出物中最有价值的一张对照表，至少包含以下行：
     - 全局人格 / system prompt：OpenClaw `SOUL.md` vs Claude Code 全局 `~/.claude/CLAUDE.md`
     - 用户画像 / 项目记忆：OpenClaw `USER.md` vs Claude Code 项目 `./CLAUDE.md`
     - 子专家：OpenClaw `AGENTS.md` vs Claude Code Subagent (`.claude/agents/*.md`)
     - 工具：OpenClaw `TOOLS.md` vs Claude Code Skill + MCP
     - 生命周期拦截：OpenClaw 事件驱动钩子 vs Claude Code Hooks
     - 打包分发：OpenClaw（暂无统一方案，看构建篇怎么说）vs Claude Code Plugin
- **产出**：
  - `notes/phase-2/my-claw/skills/skill-a/`、`skill-b/`
  - `comparison.md` 这张对照表定稿

---

## Week 8 · mini-agent-v2（动手收尾）

### 任务 18：加 subagent 能力（4 小时）

- **类型**：核心编码
- **做什么**：
  1. 复制 `mini-agent-v1/` → `mini-agent-v2/`
  2. 实现 `spawn_subagent(task, tools, budget)` 工具，**显式以 Claude Code 官方 Subagent API 形状为参考**（参见 Week 5 任务 6 / Week 6 任务 13）：
     - `task`：自然语言任务描述（喂给子 agent 的第一条 user message）
     - `system_prompt`：子 agent 的专属 system prompt
     - `tools`：工具白名单（必须是父 agent 工具集的子集）
     - `model` / `budget`：可选——模型选择 / token + 调用次数预算
     - 上下文隔离：子 agent 自带一份 conversation history，父 agent 只看到最终 `result` 字段
  3. 跑一个测试："给这个目录写 README"，子 agent 并行做 3 个子任务（扫目录 / 读 package / 读核心代码），父 agent 汇总
- **产出**：
  - `mini-agent-v2/` commit：`feat: add subagent support`
  - `mini-agent-v2/traces/subagent-readme.md` 存一次完整 transcript

---

### 任务 19：实现简化版 `/compact`（4 小时）

- **类型**：核心编码
- **资源参考**：
  - Anthropic context engineering 博客的"compaction"策略
  - Week 4 任务 3/4 + Week 6 任务 13 已读的 Claude Code 逆向资料
- **做什么**：
  1. 实现三层压缩：
     - **微观**：旧 `tool_result` 替换为占位符（`[tool_result #42 elided, 1.2KB]`）
     - **大文件剥离**：摘要前剥离超长 blob（>2KB 的字符串），用引用替换
     - **全局摘要**：调用 LLM 生成 9 段式结构化摘要（用户目标 / 已完成 / 未完成 / 关键决策 / 关键文件 / 未解决问题 / 下一步 / 用户偏好 / 注意事项），注入新会话
  2. 暴露 `/compact [focus]` 命令（`focus` 可选，用于让摘要偏向某个方面）
  3. 在一个长对话（>100k token）里测试压缩比 + 压缩后续聊是否还能记得关键事实
- **产出**：
  - `mini-agent-v2/` commit：`feat: add /compact command with 3-layer compression`
  - 压缩前/后 token 对比日志存到 `mini-agent-v2/traces/compact-experiment.md`

---

### 任务 20：对比报告（2 小时）

- **类型**：总结
- **做什么**：把整周累积的 `comparison.md` 整理成一份完整对比报告，**项目列 4 个**（mini-agent v1 / v2 / OpenClaw / Claude Code），**维度 6 个**：
  1. Context 管理（compact / 全局摘要 / context isolation 怎么做）
  2. Tool 抽象（zod schema / json schema / 工具白名单与传递）
  3. Sub-agent 支持（是否有、调度形态、上下文隔离粒度）
  4. **扩展点抽象**（subagent / skill / hook / plugin 各自如何映射——这一列是新维度，承接 Week 7 任务 17）
  5. Safety & Sandboxing（Phase 2 只是知道概念，Phase 4 会补）
  6. Persistence（session / memory / SKILL.md 类长期记忆）
- **产出**：`notes/phase-2/comparison.md` 最终版

---

## 可选补充（时间富裕再做）

- B 站陈成（云谦）《打造生产级 Coding Agent：架构、挑战与解决方案》 https://www.bilibili.com/video/BV1KQSYBhEuN/ （蚂蚁 Neovate Code 架构分享，生产视角）
- Boris Cherny（Claude Code 作者）访谈：https://every.to/podcast/how-to-use-claude-code-like-the-people-who-built-it （英文，但作者级细节）
- B 站慢学主义 AI 产品经理《不要构建多 agent 系统：精读 Cognition》https://www.bilibili.com/video/BV1GG2aBtE1t/
- Claude Agent SDK 编程接入（如果未来想从代码侧 spawn Claude Code 实例）：https://code.claude.com/docs/en/agent-sdk —— 本路径不强制，可放到 Phase 4 末尾或弹性周

## 明确**不要**读的

- 任何知乎/腾讯云/CSDN 上的"OpenClaw 完全指南 3.2W 字"类文章（洗稿矩阵）
- 任何"Hermes Agent 20 万字深度解析"类文章
- B 站 UP 主"酌沧"的视频（疑似 AI 合成播报）
- B 站标题含"最新版 / 吃透 / 少走 99% 弯路"的
- Matt Pocock / 吴恩达 / LangChain 1.0 这几个视频的**中文搬运版**（实际是英文），要看就去 YouTube 看原版（但本路径硬性要求视频中文，所以直接跳过）
- Cline / opencode 的中文"深度解读"——本阶段已经不再覆盖这两个项目，遇到只当作"另一个 TS coding agent 样本"瞟一眼即可

---

## 阶段产出物清单

- [ ] `notes/phase-2/mini-agent-v2/`：支持 subagent + `/compact`
- [ ] `notes/phase-2/comparison.md`：4 个项目（v1 / v2 / OpenClaw / Claude Code）横向对比，6 个维度
- [ ] `notes/phase-2/my-cc-skills/`：≥1 个你写的 Claude Code skill
- [ ] `notes/phase-2/my-cc-hooks/`：≥1 个你写的 PreToolUse hook
- [ ] `notes/phase-2/my-cc-plugin/`：把 skill + hook 打包成的 plugin
- [ ] `notes/phase-2/my-claw/`：一个跑起来的 OpenClaw 实例（≥2 个 Channel）+ 你自己写的 ≥2 个 Skill
- [ ] 至少 15 次 git commit（v2 + 周边产物相关）

---

## 自检清单（进入 Phase 3 前必须全过）

- [ ] 能徒手画一个 coding agent 的 5 层架构图
- [ ] 能解释 `/compact` 为什么要分层，每一层解决什么问题
- [ ] 能解释 Claude Code **subagent / skill / hook / plugin** 四种扩展点各自解决什么 + 不能相互替代的原因
- [ ] 能 5–10 行讲清 prompt cache 分段策略 / 四层权限 / 工具白名单传递 / context window 软硬上限
- [ ] 能说出"subagent 什么时候值得用、什么时候不值得"
- [ ] 读 Claude Code 逆向文章不再需要频繁查术语
- [ ] 能把 OpenClaw 的 SOUL / USER / AGENTS / TOOLS 和 Claude Code 的 CLAUDE.md + Skills + Subagent + Hooks + Plugins 一一映射上
- [ ] 你的 mini-agent-v2 能在 100k+ token 对话里稳定工作

---

## 过渡到 Phase 3

Phase 3 要你从 coding 场景抽身，用 Mastra / deepagentsjs 做通用 agent，并实现一个简化版 Hermes SKILL 闭环。本阶段 Week 5 + Week 7 学到的 Skill 设计将直接喂给 Phase 3 的 Hermes SKILL MVP。

进入 [`phase-3.md`](./phase-3.md)。
