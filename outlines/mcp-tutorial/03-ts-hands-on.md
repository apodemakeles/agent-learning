# 03 · TS 实操：写一个 MCP Server（~2h）

> 目标：亲手写一个能跑的 MCP Server，连上 Claude Desktop 验证，用 Inspector 看到真实的 JSON-RPC 报文。建立从协议到代码的完整手感。

---

## 前置条件与时间预算

- **前置**：完成 01-02 章；Node.js 18+ 已安装；有 Claude Desktop（用于测试连接）
- **时间**：2 小时
- **节奏建议**：1h 跟 QuickStart + 30min 加 Resource + 15min Inspector + 15min 视频

---

## 任务清单

### 任务 1：跟官方 QuickStart 写一个天气 MCP Server（1 小时）

- **类型**：动手实操
- **时间**：1 h
- **资源**：
  - MCP 官方 QuickStart（TypeScript 版）：https://modelcontextprotocol.io/quickstart/server（选 TypeScript tab）
  - 如果英文文档卡住，对照看：[Anthropic 官方 MCP 入门课程（B站中英字幕）](https://www.bilibili.com/video/BV1AkcQzzE3x/)
- **做什么**：
  1. 按 QuickStart 一步步来：
     - 初始化 Node.js 项目
     - 安装 `@modelcontextprotocol/sdk`
     - 实现一个天气查询 Tool（调用公开天气 API）
     - 配置 Claude Desktop 连接（编辑 `claude_desktop_config.json`）
  2. 传输模式用 **stdio**（本地开发默认，Claude Desktop 会把你的 Server 当子进程启动）
  3. 在 Claude Desktop 里实际问一个天气问题，确认你的工具被调用了
- **产出**：`notes/mcp/weather-mcp-server/`——可运行的代码目录
- **踩坑预警**：
  - 如果 Claude Desktop 里看不到你的工具，**先检查** `claude_desktop_config.json` 的路径配置是否正确（绝对路径）
  - 如果启动报错，检查 `node` 命令路径——Claude Desktop 启动子进程时用的 PATH 可能和你终端里不一样
  - QuickStart 可能用到的天气 API 需要注册 API Key，提前准备好

---

### 任务 2：给你的 Server 加一个 Resource（30 分钟）

- **类型**：动手实操
- **时间**：30 min
- **资源**：
  - MCP 官方文档 Resources 章节：https://modelcontextprotocol.io/docs/concepts/resources
- **做什么**：
  1. 在你的天气 Server 上加一个 Resource，比如暴露一份「支持的城市列表」数据
  2. Resource 用 URI 标识（如 `weather://cities`），返回结构化的城市列表
  3. 在 Claude Desktop 里测试：模型能否读取你的 Resource 作为上下文？
  4. 体会 **Tool vs Resource 的实际差异**：
     - Tool = 模型**主动调用**的函数（「帮我查北京天气」→ 调用 Tool）
     - Resource = 模型**读取**的上下文数据（「看看有哪些城市可以查」→ 读取 Resource）
     - 这个区分在第 05 章做 Java 集成时非常关键：你的微服务有些接口是「查询型」的（适合 Resource），有些是「操作型」的（适合 Tool）
- **产出**：更新 `notes/mcp/weather-mcp-server/`，追加 Resource 实现

---

### 任务 3：用 MCP Inspector 调试（15 分钟）

- **类型**：动手实操
- **时间**：15 min
- **资源**：https://modelcontextprotocol.io/docs/tools/inspector
- **做什么**：
  1. 按文档安装 MCP Inspector（官方调试工具，一个 Web UI）
  2. 用 Inspector 连上你的 Server，查看：
     - `tools/list` 返回了什么（你的天气查询 Tool 的描述）
     - `resources/list` 返回了什么（你的城市列表 Resource）
  3. 在 Inspector 里手动发一次 `tools/call`，观察完整的 JSON-RPC 报文
  4. 对照第 02 章学的生命周期——你能看到 `initialize` → `initialized` → `tools/list` → `tools/call` 的完整流程
- **产出**：在 `notes/mcp/02-protocol.md` 追加一节「Inspector 实测报文」，贴 1-2 条你看到的真实 JSON-RPC 消息，并标注这是生命周期的哪个阶段

---

### 任务 4：看一个从零到发布的完整流程视频（15 分钟）

- **类型**：视频
- **时间**：15 min
- **资源**：[技术爬爬虾《从零编写MCP并发布上线，超简单！手把手教程》](https://www.bilibili.com/video/BV1RNTtzMENj/)（13 万播放，16 分钟）
- **做什么**：1.5x 速度看，重点看他的**发布流程**（npm publish）。你不需要真的发布，但了解一下 MCP Server 的分发机制：
  - npm 包：最常见的分发方式，用户通过 `npx` 直接运行
  - Docker 容器：适合需要系统依赖的 Server
  - 直接配置本地路径：开发阶段用
  - 这些分发方式对理解第 04 章的生态有帮助
- **产出**：无

---

## 自检清单（进入 04 章前必须全过）

- [ ] 你的 MCP Server 能在 Claude Desktop 中被正确调用，返回天气数据
- [ ] 你的 Resource 能被模型读取到
- [ ] 能在代码层面说出 Tool 和 Resource 的实现差异
- [ ] 用 Inspector 看过至少一条完整的 JSON-RPC 报文，并能对应到第 02 章学的生命周期阶段
- [ ] 知道 MCP Server 的三种分发方式（npm / Docker / 本地路径）

---

## 过渡到 04 章

你已经有了从零写 MCP Server 的手感。下一章快速扫一下生态全景——有哪些现成的 Server、谁在用——然后进入最终章的 Java/Spring 实战。

进入 [`04-ecosystem.md`](./04-ecosystem.md)。
