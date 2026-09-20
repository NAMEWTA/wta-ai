# wta-ai

独立的 AI Agent 平台，包含管理控制台、开发者文档门户、Python 服务端，以及自己的构建、发布和部署资产。

项目仓库：[NAMEWTA/wta-ai](https://github.com/NAMEWTA/wta-ai)。WTA-plus 和其他业务系统可通过公开的运行与管理 API 接入。

## 当前状态

项目已初始化 Speculo，并从 [WTA-plus #3](https://github.com/NAMEWTA/WTA-plus/issues/3) 承接设计 change。已有设计共识；前后端和部署实现尚未开始，当前目录中的说明文件用于明确归属，不代表已有可运行服务。

当前 change：[2026-09-19-go-python-ai-platform](speculo/.speculo/specdev/changes/2026-09-19-go-python-ai-platform/)。名称保留来源追踪意义，当前技术方案不包含 Go 网关。下一步为 S-spec。

## 项目布局

| 路径 | 职责 |
| --- | --- |
| [frontend/](frontend/) | Vue 工作区，Console 与 Developer Portal 两个 App |
| [backend/](backend/) | Python API、Agent/Graph/Team 执行、知识摄取、数据与迁移 |
| [release-artifacts/](release-artifacts/) | 本项目的镜像、前端静态产物、Compose 和发布配置合同 |
| [scripts/](scripts/) | 本项目的开发、检查、构建、发布和部署脚本 |
| [speculo/](speculo/) | Speculo 工具、工作流与当前设计 change |

## 已确认方案

- 后端：Python 3.12、FastAPI、LangGraph、Celery/Redis。
- 数据与知识：PostgreSQL/pgvector、Alembic、Docling、独立 S3 接口。
- 前端：Vue 3、TypeScript、Vite、Vue Flow、Scalar。
- 产品：Agent/Graph/Team、完整 RAG、短期与长期记忆、受控 MCP/Skill 扩展、双前端与双 v1 API。
- 身份：平台超管与受限调用密钥；记忆按可信业务后端声明的业务用户隔离。
- 交付：独立 Docker Compose 单机私有化；容量、恢复与依赖兼容等待实际验证。

当前设计以 change 中的 [ADR](speculo/.speculo/specdev/changes/2026-09-19-go-python-ai-platform/ADR.md) 为准；ADR-021 更新为独立项目与仓库。历史提案保留在 LOG 中。

## 设计工件检查

在本项目根目录执行：

```bash
speculo doctor .
node speculo/workflows/specdev/common/tools/validate-specdev.mjs --self-check
node speculo/workflows/specdev/common/tools/validate-specdev.mjs --stage grill --repo . speculo/.speculo/specdev/changes/2026-09-19-go-python-ai-platform
```

以上检查验证工具与设计工件，不验证产品运行能力。产品开发、测试和构建命令将在实现阶段建立。
