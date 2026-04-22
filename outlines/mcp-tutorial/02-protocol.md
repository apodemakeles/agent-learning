# 02 · 协议深潜（~1h）

> 目标：能看懂 MCP 的线上报文，理解传输层选型逻辑，知道安全风险在哪。读完后能描述一次完整的 Client-Server 交互流程。

---

## 前置条件与时间预算

- **前置**：完成 01-concept，理解 Host/Client/Server 架构和三大原语
- **时间**：1 小时
- **节奏建议**：10 分钟视频 + 25 分钟读规范 + 15 分钟视频 + 10 分钟读安全规范

---

## 任务清单

### 任务 1：底层原理抓包视频（10 分钟）

- **类型**：视频
- **时间**：10 min
- **资源**：[技术爬爬虾《MCP是怎么对接大模型的？抓取AI提示词，拆解MCP的底层原理》](https://www.bilibili.com/video/BV1P3XTYPEJm/)（9.7 万播放，7 分钟）
- **做什么**：这期视频的核心价值在于他**真的抓包看了 MCP 通信过程**，不是照念文档。重点看：
  1. 他拦截到的 JSON-RPC 报文长什么样
  2. Client 和 Server 之间的消息来回顺序
  3. 工具描述是怎么从 Server 传给 Client 的
- **产出**：无，为下面的规范阅读建立直觉

---

### 任务 2：读 MCP 官方规范——生命周期 + 传输层（25 分钟）

- **类型**：核心阅读
- **时间**：25 min
- **资源**：
  - 生命周期：https://modelcontextprotocol.io/specification/2025-03-26/basic/lifecycle
  - 传输层：https://modelcontextprotocol.io/specification/2025-03-26/basic/transports
- **做什么**：

  **生命周期**（15 min）：
  1. 精读 `initialize` → `initialized` → 正常通信 → `shutdown` 的完整流程
  2. 搞清楚**能力协商**（Capability Negotiation）机制——Client 和 Server 各声明自己支持什么（比如 Server 声明支持 Tools 但不支持 Prompts），不支持的能力就不会被使用
  3. 理解三种消息类型：
     - **Request**（带 `id`，期望对方响应）
     - **Response**（携带对应 `id` 的结果）
     - **Notification**（无 `id`，单向通知，不期望响应）

  **传输层**（10 min）：
  1. **stdio**：Host 把 Server 当子进程启动，通过 stdin/stdout 传 JSON。零配置、零网络，只能本地用。你在 03 章 TS 实操会用这个模式
  2. **Streamable HTTP**（当前推荐的远程传输）：单一 HTTP 端点（如 `/mcp`），Client POST JSON-RPC 请求，Server 可返回普通 JSON 或 SSE 流式响应。支持断线重连。你在 05 章 Java 实战会用这个模式
  3. **HTTP+SSE（已废弃）**：两个端点（GET 建 SSE 连接 + POST 发请求），不支持断线重连，服务端状态管理复杂——知道它被废弃了就行，不用深入

  **选型决策**：本地开发 → stdio；远程/生产 → Streamable HTTP。完毕。

- **产出**：创建 `notes/mcp/02-protocol.md`，包含：
  - 用自己的话写一个生命周期时序（文字描述即可，不需要画图工具）
  - 传输层选型决策总结：什么时候用什么、为什么

---

### 任务 3：马克的批判性思考视频（15 分钟）

- **类型**：视频
- **时间**：15 min
- **资源**：[马克的技术工作坊《为什么越来越多的人抛弃 MCP，转向 CLI？》](https://www.bilibili.com/video/BV16zDfBtECQ/)（12 万播放，15 分钟）
- **做什么**：这期视频的价值不在技术细节，而在**批判性思考**——MCP 不是银弹。看完后想：
  1. 马克提出的问题（MCP 的开发复杂度、调试困难、安全风险），哪些对你的 SaaS 场景确实存在？
  2. 对于你的微服务集成目标，MCP 的优势是否仍然成立？（提示：你的场景是远程服务调用，不是本地 CLI，所以 MCP 相对 CLI 的优势更明显）
- **产出**：在 `notes/mcp/02-protocol.md` 追加一节「MCP 的局限性」，列 2-3 条你认同的批评

---

### 任务 4：安全模型速读（10 分钟）

- **类型**：核心阅读
- **时间**：10 min
- **资源**：https://modelcontextprotocol.io/specification/2025-03-26/basic/security
- **做什么**：
  1. 读官方安全规范，重点抓三条：
     - 工具调用需用户显式同意（Human-in-the-Loop）
     - HTTP 传输强制 TLS
     - 远程服务器使用 OAuth 2.1 认证
  2. 理解 **Tool Annotations**——`readOnlyHint`、`destructiveHint`、`idempotentHint`。这个机制对你的微服务暴露 MCP 直接相关：查询接口标 `readOnlyHint: true`，删除接口标 `destructiveHint: true`，Client 会据此决定是否需要人工确认
  3. 知道最大安全风险是**间接提示注入**（Indirect Prompt Injection）：工具返回的内容可能携带恶意指令，反向注入模型。攻击路径举例：恶意网页内容通过浏览器 MCP Server 返回给模型，指令模型执行危险操作
  4. 缓解原则：所有工具响应视为不可信输入、最小权限设计、沙箱运行 Server
- **产出**：在 `notes/mcp/02-protocol.md` 追加一节「安全要点」，列出对自己微服务场景最相关的 3 条安全考量

---

## 自检清单（进入 03 章前必须全过）

- [ ] 能说出 JSON-RPC 2.0 的三种消息类型（Request / Response / Notification）及其区别
- [ ] 能描述一次完整的 MCP 会话生命周期（从 `initialize` 到 `shutdown`）
- [ ] 能解释 stdio 和 Streamable HTTP 各自适用什么场景
- [ ] 能说出 MCP 的至少一个安全风险（间接提示注入）和对应的缓解措施
- [ ] 能说出 MCP 的至少一个局限性

---

## 过渡到 03 章

协议层已经理解了，下一章动手——用 TypeScript 从零写一个 MCP Server，连上 Claude Desktop 测试，亲眼看到你学的那些 JSON-RPC 报文在真实场景中跑起来。

进入 [`03-ts-hands-on.md`](./03-ts-hands-on.md)。
