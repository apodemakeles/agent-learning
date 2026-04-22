# MCP 教程设计文档

> 日期：2026-04-22
> 状态：已批准

## 定位

独立的 MCP 学习路径执行手册，放在 `outlines/mcp-tutorial/`，与现有 `outlines/agent-learning/` 平级但独立。格式对齐现有 Phase 文件（任务清单 + 外部资源链接 + 产出物 + 自检）。

## 受众与约束

- **受众**：自己（有 AI Agent 基础）
- **时间预算**：8 小时
- **资源策略**：中文视频建立直觉，官方英文文档做技术精读。不自己输出教程内容，指向外部资料
- **代码语言**：TS 做基础实操（03 章），Java/Spring 做最终章实战（05 章）
- **Java 集成深度**：架构方案 + 可跑 Demo 都要

## 文件结构

```
outlines/mcp-tutorial/
├── README.md              ← 总纲（定位、时间预算、资源标准）
├── 01-concept.md          ← 概念与定位（~1h）
├── 02-protocol.md         ← 协议深潜（~1h）
├── 03-ts-hands-on.md      ← TS 实操（~2h）
├── 04-ecosystem.md        ← 生态速览（~0.5h）
└── 05-java-spring.md      ← Java/Spring 实战（~3.5h）
```

产出物目录：`notes/mcp/`

## 各章设计

### 01-concept.md（~1h）

| 任务 | 类型 | 时间 | 资源 |
|------|------|------|------|
| 破冰视频 | 视频 | 15min | 隔壁的程序员老王《10分钟讲清楚 Prompt, Agent, MCP》 |
| 概念深化 | 视频 | 15min | 轩辕的编程宇宙《MCP到底是什么》 |
| 官方文档精读 | 阅读 | 20min | MCP Introduction + Architecture |
| 对比思考 | 笔记 | 10min | MCP vs FC vs A2A |

产出：`notes/mcp/01-concepts.md`

### 02-protocol.md（~1h）

| 任务 | 类型 | 时间 | 资源 |
|------|------|------|------|
| 底层抓包视频 | 视频 | 10min | 技术爬爬虾《拆解MCP底层原理》 |
| 规范精读 | 阅读 | 25min | MCP Lifecycle + Transports 规范 |
| 批判思考视频 | 视频 | 15min | 马克《为什么越来越多人抛弃MCP转向CLI》 |
| 安全模型速读 | 阅读 | 10min | MCP Security 规范 |

产出：`notes/mcp/02-protocol.md`

### 03-ts-hands-on.md（~2h）

| 任务 | 类型 | 时间 | 资源 |
|------|------|------|------|
| 跟官方 QuickStart 写天气 Server | 实操 | 1h | MCP QuickStart TS 版 |
| 加一个 Resource | 实操 | 30min | MCP Resources 文档 |
| MCP Inspector 调试 | 实操 | 15min | MCP Inspector 文档 |
| 发布流程视频 | 视频 | 15min | 技术爬爬虾《从零编写MCP并发布上线》 |

产出：`notes/mcp/weather-mcp-server/`

### 04-ecosystem.md（~0.5h）

| 任务 | 类型 | 时间 | 资源 |
|------|------|------|------|
| 全景视频 | 视频 | 15min | 飞天闪客《拆穿Skill/MCP/RAG/Agent/OpenClaw》 |
| 浏览生态仓库 | 阅读 | 15min | awesome-mcp-servers + 官方 servers |

产出：`notes/mcp/04-ecosystem.md`（含 3 个 SaaS 微服务 MCP 候选）

### 05-java-spring.md（~3.5h）

| 任务 | 类型 | 时间 | 资源 |
|------|------|------|------|
| Spring AI MCP 文档精读 | 阅读 | 45min | Spring AI MCP Overview + Server Starter + Annotations |
| 中文视频参考 | 视频 | 15min | 程序员Cafe《SpringAI开发Java版mcp服务》 |
| 架构设计 | 设计 | 30min | 网关层统一 MCP Server 方案 |
| 动手写 Spring Boot MCP Server | 实操 | 1.5h | spring-ai-examples + MCP QuickStart Java 版 |
| 回顾与落地清单 | 笔记 | 30min | Spring 官方博客（可选） |

产出：`notes/mcp/05-java-spring.md` + `notes/mcp/spring-mcp-demo/`

## 关键设计决策

1. **网关层统一 MCP Server**（而非每个微服务独立暴露）——集中管理工具描述和权限，减少依赖扩散
2. **REST → MCP Tool 不做一对一映射**——按 AI Agent 使用意图重新组织
3. **正反对读**——02 章放了马克「抛弃 MCP 转向 CLI」的批判视频
4. **Java/Spring 中文资源空白**——该章以英文官方文档为主，中文视频仅辅助
