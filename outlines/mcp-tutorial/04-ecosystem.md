# 04 · 生态速览（~0.5h）

> 目标：快速建立 MCP 生态的全景视图——有哪些现成的 Server、谁在用、行业走向如何。重点产出：从你的 SaaS 系统中选出 3 个适合做 MCP Server 的微服务候选。

---

## 前置条件与时间预算

- **前置**：完成 01-03 章
- **时间**：30 分钟
- **节奏建议**：15 分钟视频 + 15 分钟浏览仓库

---

## 任务清单

### 任务 1：全景视频——MCP 在技术栈中的位置（15 分钟）

- **类型**：视频
- **时间**：15 min
- **资源**：[飞天闪客《一口气拆穿Skill/MCP/RAG/Agent/OpenClaw底层逻辑》](https://www.bilibili.com/video/BV1ojfDBSEPv/)（73 万播放，14 分钟）
- **做什么**：1.5x 看。这期视频把 MCP 放在 Skill / RAG / Agent / OpenClaw 的大图里讲，帮你理解 MCP 在整个 AI Agent 技术栈中的位置。不需要逐字听，抓他的**分类框架**：哪些技术解决「模型能力增强」，哪些解决「模型连接外部」，MCP 属于哪一类
- **产出**：无

---

### 任务 2：浏览 awesome-mcp-servers + 官方 servers 仓库（15 分钟）

- **类型**：阅读 + 整理
- **时间**：15 min
- **资源**：
  - 社区精选列表：https://github.com/punkpeye/awesome-mcp-servers （20000+ Star，收录 500+ Server）
  - 官方参考实现：https://github.com/modelcontextprotocol/servers （84000+ Star）
- **做什么**：

  **第一个 5 分钟**——扫 awesome 列表的分类：
  - 数据库类（Postgres、SQLite、MongoDB…）
  - 浏览器/爬虫类（Playwright、Puppeteer…）
  - 云服务类（AWS、Cloudflare、Stripe…）
  - 开发工具类（GitHub、Git、Docker…）
  - 文件系统类（本地文件、Google Drive…）
  - 建立「MCP 生态有什么」的直觉

  **第二个 5 分钟**——看官方 servers 仓库：
  - 进 `src/` 目录，挑一个你感兴趣的（推荐 `postgres` 或 `filesystem`）
  - 看它的代码结构——和你第 03 章自己写的天气 Server 有什么共同模式？（都是 `server.setRequestHandler` 注册 Tool/Resource）

  **第三个 5 分钟**——想你自己的场景：
  - 你的 SaaS 系统里哪些微服务适合做成 MCP Server？
  - 判断标准：AI Agent 使用频率高、接口相对标准化、数据敏感度可控
  - 列 3 个候选

- **产出**：创建 `notes/mcp/04-ecosystem.md`，包含：
  - MCP 生态分类概览（5-8 个类别，每类 1-2 个代表项目）
  - 你的 SaaS 系统中 3 个 MCP 候选微服务，每个写：服务名 + 为什么适合 + 暴露什么 Tool/Resource
  - 已有落地的关键信号（一句话列出）：Claude Desktop、Cursor、VS Code Copilot、OpenAI Agents SDK、JetBrains 等已接入 MCP

---

## 明确**不需要**做的

- 不需要深入研究每个开源项目的实现细节
- 不需要整理完整的 MCP Server 列表——awesome 仓库已经做了这件事
- 不需要对比每个 Server 的优劣——这章是建立全景直觉，不是做技术选型

---

## 不设自检

这章是阅读整理型，没有硬性知识点需要验证。核心产出是那 3 个候选微服务——这个思考直接衔接第 05 章的 Java/Spring 实战。

---

## 过渡到 05 章

生态全景已经有了，你也选好了候选微服务。最终章——用 Spring AI MCP 把你的 REST Controller 包装成 MCP Server，从架构设计到可跑 Demo。

进入 [`05-java-spring.md`](./05-java-spring.md)。
