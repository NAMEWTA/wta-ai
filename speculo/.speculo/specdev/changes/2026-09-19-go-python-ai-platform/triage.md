---
schema_version: 1
artifact: triage
change: 2026-09-19-go-python-ai-platform
mode: intake
source: <Path>{roots.state}/specdev/changes/2026-09-19-go-python-ai-platform/source.md</Path>
classification: feature
risk: high
route: specdev/spec
ready_for_implementation: false
external_action: closed
publish_action: not-requested
publish: null
updated_at: 2026-09-20T14:52:57.604304+00:00
---

# Triage: 独立 AI 应用平台（G 已达成共识）

## 当前判定

- **影响：** 独立 wta-ai 项目与公开仓库，自有 frontend/backend/release-artifacts/scripts、双前端、运行/第三方管理 API、超管与密钥、完整 RAG/记忆/扩展与执行/存储/交付合同，风险仍为 high。
- **当前行为：** 已按 LOG-051 / ADR-021 转入 wta-ai；初始设计及 LOG/ADR/CONTEXT 已完成，用户在 LOG-050 确认整体共识；G 完成，交接 S-spec，当前未形成实现 Ready。
- **当前证据：** Issue #3 原始快照保持不变；最新用户要求、联网一手研究与当前方案记录在 <Path>{roots.state}/specdev/changes/2026-09-19-go-python-ai-platform/LOG.md</Path>。
- **历史转入：** 2026-09-19 用户要求“这个先不做，另开一个change，等我想清楚”，本 change 成为 Issue #3 唯一归属；当前恢复已取代暂停条件，保留该转入历史，不重复 intake。

## 未知项

- **可发现事实：** 已核对 AI Maven 占位、独立服务与发布清单；已研究 Dify/Flowise/Langflow/LangGraph/Temporal 等一手资料，证据位于当前 LOG。
- **已确认：** P0 包含完整 RAG/记忆/高级扩展；独立超管登录与密钥分发，无租户和普通用户共享体系，与 WTA 只经网络接口交互；已接受 LangGraph 技术基线与独立 PostgreSQL/Alembic 作用域；允许配置出网和受控写工具，Skill 不执行任意代码。
- **第二轮已确认：** 三类验收主线、统一 Callable/Deployment/Run、指南公开而服务文档与试调鉴权。
- **第三轮已确认：** 分类可配默认主动删除；Docker Compose 单机私有化先实测；超管集中管理/分发密钥；暂不建设第三方身份与审批集成；硬预算和发布门禁；逐 token 持久化完整重放；双 v1 HTTP 合同。
- **第四轮已确认：** D-031 文件/受控 URL 接入、解析/OCR/切分、混合检索/rerank、引用、更新/重建、删除的完整 RAG 范围；中文、扫描件、表格样本验收；企业网盘/数据库同步连接器暂不进入 P0。
- **第四轮补充确认：** 记忆增加外部业务用户隔离；远程 MCP、Skill 版本包管理和超管本地写审批。
- **第五轮已确认：** 可信业务后端传 external_user_id；凭据范围/应用/业务用户隔离，短期再加会话；标识必填无共享回退；自动提取默认关，超管配置自动生效或人工确认，可纠正/导出/删除。
- **共识核对：** 21 个设计节点均已回答，无未决产品节点；用户已对 LOG-049 完整摘要确认整体共识，记录见 LOG-050。容量/恢复、依赖兼容及样本效果属于后续工程验证，不作为必须由用户给出的未知。
- **低影响实现细节：** 在已确认合同内由实现者决定，不预建产品源码。

## 路由

- **下一入口：** <Path>{roots.workflows}/specdev/S-spec/S-spec.md</Path>。
- **交接条件：** 用户已确认整体共识，设计树 consensus；G 完成记录见 LOG-050。
- **当前行为：** G 完成，current_work 已清空，works_run 包含 specdev/grill-with-docs；下一 Work 为 specdev/spec，尚未自动执行。

## 外部动作

- **远程目标：** <Url>https://github.com/NAMEWTA/WTA-plus/issues/3</Url>。
- **关闭能力：** supported。
- **当前状态：** closed；已于 2026-09-20T14:51:41Z 关闭，原因 not_planned（工作转入独立项目，不代表产品完成）。
- **授权记录：** 2026-09-20 用户明确授权创建 wta-ai public 仓库、初始化并迁移本 change、评论说明去向并关闭原 issue。
- **尝试与结果：** 新仓 public、main 已推送并回读设计文件；迁移说明已发布，issue 已关闭。REST 回读 state=closed；说明见 <Url>https://github.com/NAMEWTA/WTA-plus/issues/3#issuecomment-5750538523</Url>。

## 发布投影

publish_action=not-requested，未创建发布账本。
