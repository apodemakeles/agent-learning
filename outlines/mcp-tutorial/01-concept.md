# 01 · 概念与定位（~1h）

> 目标：理解 MCP 是什么、解决什么问题、和 Function Calling / A2A 的关系。读完后能清晰回答「为什么我们的 SaaS 微服务应该考虑暴露 MCP 而不只是 REST API」。

---

## 前置条件与时间预算

- **前置**：理解 AI Agent 的 tool-use 机制（知道什么是 Function Calling）
- **时间**：1 小时
- **节奏建议**：30 分钟看视频 + 20 分钟读文档 + 10 分钟写笔记

---

## 任务清单

### 任务 1：破冰视频（15 分钟）

- **类型**：视频
- **时间**：15 min
- **资源**：[隔壁的程序员老王《10分钟讲清楚 Prompt, Agent, MCP 是什么》](https://www.bilibili.com/video/BV1aeLqzUE6L/)（78 万播放，11 分钟）
- **做什么**：1.5x 看完。重点抓他对 MCP 的定位——MCP 在 Prompt 和 Agent 这两个概念之间处于什么位置？
- **产出**：无，热身

---

### 任务 2：概念深化视频（15 分钟）

- **类型**：视频
- **时间**：15 min
- **资源**：[轩辕的编程宇宙《MCP到底是什么，一个动画彻底搞懂！》](https://www.bilibili.com/video/BV1sWoMYUEi9/)（19 万播放，10 分钟）
- **做什么**：
  1. 重点看 Host / Client / Server 三层架构的动画演示
  2. 注意 Tools / Resources / Prompts 三大原语的区分——这三个概念贯穿后续所有章节
  3. 看完后想一下：这个架构和你已经理解的 Function Calling 有什么结构性差异？
- **产出**：无

---

### 任务 3：读 MCP 官方文档——Introduction + Architecture（20 分钟）

- **类型**：核心阅读
- **时间**：20 min
- **资源**：
  - https://modelcontextprotocol.io/introduction
  - https://modelcontextprotocol.io/docs/concepts/architecture
- **做什么**：
  1. 对照视频建立的直觉，精读官方定义——视频给你的印象和官方文档有哪里对不上？
  2. 画一张 Host → Client → Server 的关系图（手画或文字描述都行）
  3. 搞清楚三大原语各自的用途边界：
     - **Tools**：模型主动调用的函数（类似 Function Calling，但标准化了）
     - **Resources**：模型读取的上下文数据（类似 RAG 的数据源，但由 MCP Server 结构化提供）
     - **Prompts**：预定义的提示模板（让 Server 端控制交互模式）
- **产出**：创建 `notes/mcp/01-concepts.md`，包含：
  - 自己画的架构关系图（文字版即可）
  - 三大原语各一句话定义 + 一个你能想到的业务场景举例

---

### 任务 4：对比思考——MCP vs Function Calling vs A2A（10 分钟）

- **类型**：思考 + 笔记
- **时间**：10 min
- **资源**：无需新资料，基于任务 1-3 已读内容
- **做什么**：在笔记里回答三个问题：
  1. **MCP 和 Function Calling 是竞争还是互补关系？** 提示：Function Calling 定义了「模型怎么调用工具」，MCP 定义了「工具怎么被发现和连接」——它们解决的是不同层次的问题
  2. **如果你的 SaaS 微服务已经有完善的 REST API，MCP 额外提供了什么价值？** 提示：想想工具描述的标准化、动态发现、上下文注入（Resources）这些 REST API 做不到的事
  3. **MCP（模型连工具）和 A2A（Agent 连 Agent）的边界在哪？** 提示：MCP 是垂直集成，A2A 是水平编排，两者互补
- **产出**：追加到 `notes/mcp/01-concepts.md`

---

## 自检清单（进入 02 章前必须全过）

- [ ] 能说出 Host / Client / Server 各自的职责，以及它们之间的关系（Host 包含 Client，Client 连 Server）
- [ ] 能区分 Tools / Resources / Prompts 三个原语，并各举一个业务场景
- [ ] 能用一句话回答「我们的微服务为什么不能只靠 REST API，还需要 MCP」
- [ ] 能说出 MCP 和 Function Calling 的关系是互补而非竞争

---

## 过渡到 02 章

你现在知道 MCP 是什么了，下一章我们深入协议本身——看看线上跑的 JSON-RPC 报文长什么样、传输层怎么选、安全风险在哪。

进入 [`02-protocol.md`](./02-protocol.md)。
