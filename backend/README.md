# 服务端

本目录拥有 wta-ai 的 Python 服务端、数据库模型及 Alembic 迁移。当前仅有已确认设计，尚无产品代码或可执行构建入口。

采用 FastAPI、LangGraph/PostgreSQL checkpointer、Celery/Redis、PostgreSQL/pgvector、Docling；API、图执行 Worker、摄取 Worker 与分发器按已确认合同分进程运行。管理与运行分别提供版本化 OpenAPI。

平台数据库、认证与发布独立于 WTA-plus。业务 schema 与框架 checkpoint/store schema 分别明确迁移责任，不导入 WTA 的 MySQL 基座或历史 AI 数据。

具体模块边界、兼容依赖、测试和启动命令由后续 Spec 与实现确定。当前合同见 [设计 ADR](../speculo/.speculo/specdev/changes/2026-09-19-go-python-ai-platform/ADR.md)。
