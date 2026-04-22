# 05 · Java/Spring 实战（~3.5h）

> 目标：理解 Spring AI MCP 的架构设计，设计「网关层统一 MCP Server」方案，并亲手把一个 REST Controller 包装成 MCP Server，用 Claude Desktop 连接验证。

---

## 前置条件与时间预算

- **前置**：完成 01-04 章；Java 17+ 和 Maven/Gradle 已安装；有 Spring Boot 开发经验
- **时间**：3.5 小时
- **节奏建议**：1h 读文档看视频 + 30min 架构设计 + 1.5h 写代码 + 30min 回顾总结

---

## 任务清单

### 任务 1：Spring AI MCP 官方文档精读（45 分钟）

- **类型**：核心阅读
- **时间**：45 min
- **资源**：
  - MCP 总览：https://docs.spring.io/spring-ai/reference/api/mcp/mcp-overview.html
  - Server Boot Starter：https://docs.spring.io/spring-ai/reference/api/mcp/mcp-server-boot-starter-docs.html
  - MCP Annotations：https://docs.spring.io/spring-ai/reference/api/mcp/mcp-annotations-overview.html
- **做什么**：

  **分层架构（15 min）**：
  1. 搞清楚 Spring AI MCP 的三层结构：
     - `io.modelcontextprotocol.sdk:mcp`——协议层（官方 Java SDK，无框架依赖）
     - `spring-ai-mcp-server`——服务端抽象（Spring AI 封装）
     - `spring-ai-mcp-server-spring-boot-starter`——自动配置（加依赖即用）
  2. 理解 Spring AI MCP 跟第 03 章 TS SDK 的对应关系——概念一致，只是 API 风格不同

  **@Tool 注解（15 min）**：
  1. 精读 Annotations 章节——`@Tool` 注解是你改造微服务的核心手段
  2. 关键点：在已有的 `@Service` 类的方法上加 `@Tool` 注解，即可自动暴露为 MCP Tool
  3. 注意参数：`name`（工具名）、`description`（工具描述，会被传给 AI 模型）、`returnDirect`（是否直接返回给用户）

  **传输模式配置（15 min）**：
  1. **stdio**：`spring.ai.mcp.server.transport=STDIO`——适合本地调试，Claude Desktop 连接
  2. **WebMVC**：`spring.ai.mcp.server.transport=SSE`（实际走 Streamable HTTP）——适合远程部署，你的微服务生产环境用这个
  3. WebMVC 模式需要额外引入 `spring-ai-mcp-server-webmvc-spring-boot-starter`

- **产出**：在 `notes/mcp/05-java-spring.md` 记录：
  - Spring AI MCP 的分层架构（文字版）
  - `@Tool` 注解的关键参数和用法
  - stdio vs WebMVC 传输的配置差异

---

### 任务 2：中文视频参考——Spring AI MCP 跑通感受（15 分钟）

- **类型**：视频（辅助）
- **时间**：15 min
- **资源**：[程序员Cafe《基于SpringAI开发Java版mcp服务》](https://www.bilibili.com/video/BV1yT8qzMEbd/)（1 万播放，12 分钟）
- **做什么**：1.5x 速度扫一遍。这个视频提到了「改造已有 SpringBoot 微服务为 MCP 服务」，看他的思路和踩坑点。视频技术深度一般，但对你的场景直接相关——重点看他遇到了什么问题、怎么解决的
- **产出**：无，用于建立预期

---

### 任务 3：架构设计——网关层统一 MCP Server 方案（30 分钟）

- **类型**：思考 + 设计
- **时间**：30 min
- **资源**：无需新资料，基于任务 1-2 已读内容 + 第 04 章选出的 3 个候选微服务
- **做什么**：在你的 SaaS 架构背景下，设计「网关层统一 MCP Server」方案。

  **架构决策（已确认）**：在 API Gateway / BFF 层做一个统一的 MCP Server，聚合后端微服务能力。
  - 优点：集中管理工具描述和权限，避免每个微服务都维护 MCP 依赖
  - 代价：Gateway 层需要理解后端微服务的业务语义

  **需要设计的 4 件事**：

  1. **REST → MCP Tool 映射策略**：
     - **不做一对一映射**——REST 的 CRUD 端点是面向前端 UI 设计的，MCP Tool 应该按 AI Agent 的使用意图重新组织
     - 举例：把 `GET /orders/{id}` + `GET /orders/{id}/items` + `GET /orders/{id}/payments` 合并为一个 `get_order_detail` Tool，返回 Agent 做决策需要的完整订单信息
     - 举例：`POST /orders/{id}/cancel` 单独做一个 `cancel_order` Tool，因为这是一个独立的意图

  2. **Resource 设计**：
     - 哪些数据适合暴露为 Resource？——相对静态的上下文信息
     - 举例：系统配置、业务枚举值（订单状态定义）、API Schema、数据字典
     - Resource 帮助模型理解你的业务领域，不需要每次都作为 Tool 参数传入

  3. **Tool Annotations 标注**：
     - 查询类接口：`readOnlyHint: true`
     - 删除/取消类接口：`destructiveHint: true`（Client 会要求人工确认）
     - 幂等操作：`idempotentHint: true`
     - 这些标注直接影响 AI Agent 的行为安全性

  4. **认证与权限**：
     - 对外暴露的 MCP Server 使用 OAuth 2.1
     - MCP Server 调用后端微服务时，复用已有的内部认证机制（比如 service-to-service token）
     - 工具级权限：不同用户/角色能看到不同的 Tool 列表

- **产出**：在 `notes/mcp/05-java-spring.md` 追加「架构设计方案」章节，含：
  - 架构图（文字版）：外部 AI Client → MCP Server（Gateway 层）→ 内部微服务
  - 至少 3 个具体的 Tool 定义示例（名称 + 描述 + 参数 + annotations）
  - 至少 2 个 Resource 定义示例
  - 认证方案简述

---

### 任务 4：动手实操——用 Spring AI MCP 包装一个 REST Controller（1.5 小时）

- **类型**：动手实操
- **时间**：1.5 h
- **资源**：
  - Spring AI MCP 官方示例：https://github.com/spring-projects/spring-ai-examples/tree/main/model-context-protocol
  - MCP 官方 QuickStart Java 版：https://modelcontextprotocol.io/quickstart/server （切换到 Java tab）
  - MCP Java SDK：https://github.com/modelcontextprotocol/java-sdk
- **做什么**：

  **Step 1：项目搭建（15 min）**
  1. 创建 Spring Boot 项目（Spring Initializr 或手动）
  2. 引入依赖：`spring-ai-mcp-server-spring-boot-starter`
  3. 参考 `spring-ai-examples/model-context-protocol/weather/` 的 `pom.xml` 确认依赖版本

  **Step 2：写一个模拟业务的 REST Controller（15 min）**
  1. 模拟你的某个业务接口——比如一个简单的订单查询服务
  2. 写一个 `OrderService`，包含 `getOrderDetail(String orderId)` 和 `listRecentOrders(int limit)` 方法
  3. 写对应的 `OrderController`（REST 端点）——这是你已有的 HTTP API

  **Step 3：用 @Tool 暴露为 MCP Tool（15 min）**
  1. 在 `OrderService` 的方法上加 `@Tool` 注解
  2. 写好 `description`——这是模型看到的工具说明，要写得清晰（像在给一个聪明但不了解你业务的人解释）
  3. 配置 `application.yml`：`spring.ai.mcp.server.transport: STDIO`

  **Step 4：Claude Desktop 连接测试（15 min）**
  1. 打包你的 Spring Boot 应用（`mvn package`）
  2. 配置 `claude_desktop_config.json`，用 `java -jar` 启动你的 Server
  3. 在 Claude Desktop 里问一个业务问题（「帮我查一下最近的订单」），确认 Tool 被调用

  **Step 5：切换到 Streamable HTTP 模式（15 min）**
  1. 改引入 `spring-ai-mcp-server-webmvc-spring-boot-starter`
  2. 配置传输模式切换
  3. 启动后用 MCP Inspector 或 curl 向 `/mcp` 端点发送 JSON-RPC 请求，验证远程访问可用

  **Step 6：验证双协议共存（15 min）**
  1. 确认 REST API（`/api/orders/...`）和 MCP 端点（`/mcp`）在同一个 Spring Boot 应用中同时工作
  2. 这就是你的目标架构的 MVP：一个服务同时暴露 HTTP API 和 MCP 协议

- **产出**：`notes/mcp/spring-mcp-demo/`——可运行的 Spring Boot 项目
- **关键检查点**：
  - [ ] Claude Desktop 能通过 stdio 模式发现并调用你的 Tool
  - [ ] Streamable HTTP 模式下，`/mcp` 端点能正常响应 JSON-RPC 请求
  - [ ] REST API 和 MCP 端点在同一应用中共存

---

### 任务 5：回顾与落地清单（30 分钟）

- **类型**：笔记 + 思考
- **时间**：30 min
- **资源**：
  - 可选阅读：[Spring 官方博客《Blending Chat with Rich UIs with Spring AI and MCP Apps》](https://spring.io/blog/2026/03/18/mcp-apps)——展示 MCP Server 的进阶玩法（在工具响应中嵌入富 UI 组件），浏览了解即可
- **做什么**：

  **落地清单**——如果要在你的真实 SaaS 系统中推进 MCP 集成，接下来需要做的前 5 件事：
  1. 从第 04 章选出的 3 个候选微服务中，选 1 个最简单的作为 Pilot
  2. 确定网关层 MCP Server 的技术选型（独立 Spring Boot 应用 vs 嵌入现有 Gateway）
  3. 设计 Tool 描述的版本管理机制（工具描述变更如何与微服务 API 版本对齐）
  4. 搭建 MCP Server 的可观测性（日志、metrics、tracing——复用现有 Spring Boot Actuator）
  5. 安全评审：梳理每个 Tool 的 Annotations、认证链路、敏感数据过滤

  **待调研问题**——教程没有覆盖的、你需要进一步调研的：
  - 多租户场景下的 MCP 认证：不同租户看到不同的 Tool 列表？
  - MCP Server 的限流和降级：大量 AI Agent 并发调用怎么办？
  - 工具描述的国际化：Tool description 用中文还是英文？对不同模型的效果差异？
  - MCP 与现有 API Gateway（如 Spring Cloud Gateway）的集成模式
  - Spring AI MCP 的 Sampling 机制：Server 端主动请求模型推理，适用什么场景？

- **产出**：在 `notes/mcp/05-java-spring.md` 追加：
  - 「落地前 5 步」清单
  - 「待调研问题」列表

---

## 自检清单（完成整个教程）

- [ ] 你的 Spring Boot MCP Server 能在 Claude Desktop 中被调用
- [ ] 能在 stdio 和 Streamable HTTP 两种传输模式之间切换
- [ ] REST API 和 MCP 端点在同一应用中共存
- [ ] 有一份清晰的「REST → MCP Tool 映射策略」（不是一对一，而是按 Agent 意图组织）
- [ ] 有一份「网关层统一 MCP Server」的架构设计
- [ ] 有一份可执行的「落地前 5 步」清单
- [ ] 知道至少 3 个需要进一步调研的问题

---

## 恭喜完成

你现在拥有了：
1. 对 MCP 协议的完整理解（概念 → 协议 → 实操）
2. 一个 TS 版和一个 Java 版的可跑 MCP Server
3. 一份针对你的 SaaS 系统的 MCP 集成架构方案
4. 一份落地行动清单

下一步：按落地清单执行，在真实系统中 Pilot。
