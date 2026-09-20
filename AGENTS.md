# AGENTS.md

## 项目边界

- 本仓是独立 AI Agent 平台 wta-ai。前后端、依赖、数据库、构建、发布和部署均由本项目拥有。
- 源码归属为 `frontend/` 与 `backend/`；发布资产归属为 `release-artifacts/`，项目脚本归属为 `scripts/`。
- WTA-plus 是外部集成方之一，交互通过公开网络 API，不依赖其源码、Maven、私有前端包、MySQL 基座或发布流程。
- 当前只完成项目初始化与设计迁移。继续开发前读取当前 change 的 ADR、CONTEXT、设计树和 worklog，以 ADR-021 覆盖旧同仓目录决定；历史 LOG 和 source 保留来源语境。
- 设计共识不等于已有实现、运行证据或部署授权。下一 Work 为 S-spec；后续实现前建立适用于 Python/Vue 的项目工程规范及真实质量门禁。
- 验证命令必须来自真实脚本；目前产品 test/typecheck/lint/build 配置为 null。Speculo 校验不能代替产品测试。
- 本仓公开；不得提交运行秘密、真实业务数据、模型凭据、缓存或生成产物。

<!-- SPECULO-PERSISTENT-KNOWLEDGE:START -->
## Speculo 永久知识

以下路径只在当前任务相关时按需读取，不会自动激活 workflow 或 Work：

- learning：<Path>{roots.state}/learning/context/INDEX.md</Path>
- learning：<Path>{roots.state}/learning/context/REVIEW.md</Path>
- ops：<Path>{roots.state}/ops/knowledge/</Path>
- ops：<Path>{roots.state}/ops/hosts/{host_id}/knowledge/</Path>
- ops：<Path>{roots.state}/ops/projects/{project_id}/knowledge/</Path>
- specdev：<Path>{roots.state}/specdev/adr/</Path>
- specdev：<Path>{roots.state}/specdev/context/</Path>
<!-- SPECULO-PERSISTENT-KNOWLEDGE:END -->
