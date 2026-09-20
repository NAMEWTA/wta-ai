# Worklog

## Goal

本轮完成独立 wta-ai 项目初始化与迁移：建立自有前后端及发布脚本目录，运行 Speculo，保留并迁移全部设计工件，创建 public 仓库并发布初始内容，关闭 WTA-plus Issue #3 并说明去向。完成标准：文件与来源完整、新旧索引一致、当前决定和历史边界明确、工具和 schema 校验通过、远程仓库与 issue 状态可回读。产品实现仍由后续 S-spec/Tickets/Goal 推进。

## Current status

2026-09-20 用户明确改为独立 wta-ai 项目，最新归属见 LOG-051 / ADR-021；本 change 已迁入新项目，公开仓库 NAMEWTA/wta-ai 已创建并推送 main；原 WTA-plus Issue #3 已发布迁移说明并关闭，REST 回读确认。初始化、迁移和关联 issue 处理已完成，验证与远程结果见迁移证据。

2026-09-20 用户在 LOG-050 明确确认整体共识。G 已完成，design-tree.status=consensus，round=5，21 个节点全部 answered，frontier 为空；有效方案摘要见 LOG-049。works_run 已包含 specdev/grill-with-docs，current_work=null；change_status 仍 active，产品交付尚未完成。下一 Work 为 S-spec，尚未自动执行；未形成 Ready Spec/Ticket/Goal。

已接受技术与数据作用域见 <Path>{roots.state}/specdev/changes/2026-09-19-go-python-ai-platform/ADR.md</Path> 的 ADR-005～007，用户来源为 LOG-029。LOG-025～027 保留技术比较与早期产品细化，当前有效合同以 accepted ADR 和 LOG-049 摘要为准。

## Decisions

- 历史：2026-09-19 用户暂缓并从旧 change 分出 Issue #3；原始来源保留不改。本轮明确恢复取代旧暂停条件。
- 已接受（LOG-051 / ADR-021）：独立 wta-ai 项目和 public 仓库，项目根自有 <Path>frontend/</Path>、<Path>backend/</Path>、<Path>release-artifacts/</Path>、<Path>scripts/</Path>；管理控制台与开发者门户、运行及管理 OpenAPI 继续有效。
- 已接受：P0 同时包含完整 RAG、记忆与高级扩展，单 Agent 闭环只是中间里程碑。
- 已接受：平台独立超管身份与密钥分发，不建租户、普通用户私有资源及共享 ACL；与 WTA 无运行/数据/登录依赖，第三方只通过公开网络协议交互。
- 已接受：Python/FastAPI + LangGraph/PostgreSQL checkpointer + Celery/Redis + PostgreSQL/pgvector + Docling + Vue/Vue Flow/Scalar；首版不引入 Temporal、Go 或租户。
- 已接受数据库 scope：独立平台 PostgreSQL/Alembic 与 WTA Java MySQL 基座分开；实施前在新项目建立工程规范与真实门禁；旧 WTA 工程 scope 不再适用于独立平台，尚未落生产迁移，不再重复询问这一决定。
- 已接受：管理员配置出网与受控写工具，写工具经权限/审批/幂等控制；Skill 为指令、文件与能力声明，不执行任意代码，P0 不提供用户任意代码/终端/通用浏览器执行器。
- 已接受验收主线：知识问答、文档抽取、带 MCP 工具的多 Agent 任务；采用单机私有化先实测容量/恢复，不承诺未经验证的生产数值。
- 已接受：统一 Callable/Deployment/Run、不可变修订、异步受理与观察；通用指南公开、服务文档与试调鉴权。
- 已接受：数据分类可配置，默认至主动删除；逐 token 内容持久化完整重放，不能只保留最终快照；硬预算和发布质量门禁。
- 已接受：管理/运行双 v1 HTTP 合同；暂不建设第三方专门身份/审批集成，不取消公开管理 API 或本地受控写约束。
- 已接受：PDF 含扫描 OCR、DOCX/XLSX/PPTX、TXT/Markdown/HTML/CSV 与受控 URL 的完整 RAG；包含预览、混合检索/rerank、引用、更新/重建、删除，中文/扫描件/表格样本验收；暂不建设企业数据源同步连接器。
- 已接受：记忆增加外部业务用户隔离，无需平台普通用户账号；远程 MCP、Skill 版本包及超管本地写审批。
- 已接受：可信业务后端传入 external_user_id，按凭据范围+应用+业务用户（短期再加会话）隔离；标识必填无共享回退。自动提取默认关闭，超管开启自动生效或人工确认，可查看/纠正/导出/删除。
- 当前 accepted ADR 共十八项；ADR-001 由 ADR-021 替代，ADR-004/010 由 ADR-013 替代。全部节点有回答，LOG-050 整体共识与 LOG-051 明确归属修订共同构成当前决定。

## Files changed

本轮迁移：八份源工件整体转入新仓，source 逐字节保留；新增项目目录说明、SpecDev 初始化配置与迁移证据，同步新旧活动索引和 WTA 项目事实。以下列表是前一轮 G 的历史修改记录。

- 新增 <Path>{roots.state}/specdev/changes/2026-09-19-go-python-ai-platform/LOG.md</Path>：原稿/仓库/成熟平台研究、完整候选方案、逐项决定与替代关系。
- 新增 <Path>{roots.state}/specdev/changes/2026-09-19-go-python-ai-platform/ADR.md</Path>、<Path>{roots.state}/specdev/changes/2026-09-19-go-python-ai-platform/CONTEXT.md</Path>、<Path>{roots.state}/specdev/changes/2026-09-19-go-python-ai-platform/design-tree.json</Path>。
- 更新本记录、<Path>{roots.state}/specdev/changes/2026-09-19-go-python-ai-platform/triage.md</Path> 的恢复摘要与 <Path>{roots.state}/specdev/changes/2026-09-19-go-python-ai-platform/.status.json</Path>。
- 原始 source、原设计输入、其他 change、全局 active 索引、永久 ADR/context 和产品代码未修改。

## Remaining work

1. G 的初始化、访谈、共识记录与交接已完成，无待回答问题；不再次请求整体或单项共识确认。
2. 下一 Work 为 <Path>{roots.workflows}/specdev/S-spec/S-spec.md</Path>，尚未执行；以本 change 的 LOG/ADR/CONTEXT/design-tree 为输入编写行为与验收合同。
3. 下游落实工程规范 scope、依赖/镜像兼容与许可、模型/解析资源、容量/恢复/重放及删除传播验证；文档校验不作为运行证据。来源快照既有摘要偏差按 LOG-028 另行核对，原文保持不变。

## Verification

本轮初始化与迁移验证记录在 <Path>{roots.state}/specdev/changes/2026-09-19-go-python-ai-platform/evidence/project-migration.md</Path>。以下结果均为迁移前 G 阶段历史，不代表本轮重新运行产品验证。

所有命令从项目根执行；下列 Path 标签在执行时解析为真实路径。

| 命令/检查 | 退出码 | 结果 |
|---|---|---|
| `node` <Path>{roots.workflows}/specdev/common/tools/validate-specdev.mjs</Path> `--stage grill --repo .` <Path>{roots.state}/specdev/changes/2026-09-19-go-python-ai-platform/</Path> | 0 | 0 error / 0 warning；初始化结构有效，不等于设计共识 |
| 同工具、同输入，`--stage triage` | 0 | 0 error / 0 warning |
| `git diff --check` | 0 | 跟踪文件差异无空白错误；新增文件另由内联检查读取 |
| `python` 内联 JSON Schema 检查初次启动 | 1 | 当前 Python 无 jsonschema；没有修改仓库依赖 |
| `uv run --isolated --no-project --with jsonschema==4.26.0 python -`，内联只读校验 | 1 | 两份 JSON Schema、20 节点 DAG、LOG 锚点与 frontier 通过；后续 source digest 断言失败，属于下述既有偏差，未隐藏失败 |
| `python` 内联对比 source 与 `git show HEAD:<source-path>` 并重算摘要 | 0 | 文件与 HEAD 完全相同，实际正文摘要在 HEAD 中也不匹配声明值 |

**既有来源摘要偏差：** 声明 content SHA-256 为 `d9eaf812bc332c676aeaeb367f69fa43a91800727892ea6d757a94823f01e059`；按 source 声明算法重算为 `76f7629dbcfbe8b469b69ec82dba3b30bc44e042eb78bfb020192e76a07f0e2a`。详见 LOG-028。保留原 source，不通过改输入或删断言伪造通过；后续来源 reconcile 核对 capture 算法/范围。

已回读四份真实源工件，确认 accepted 技术/架构决定与节点状态一致；恢复键保持 G。未执行产品构建、数据库迁移、真实模型、LangGraph/Celery 故障注入或部署，平台可靠性尚未验证。初始化完成且技术基线已接受，整体行为共识和生产验收仍是后续工作。

**D-010 接受后的复验（2026-09-20）：** 同一 grill 与 triage 命令均 exit 0，零错误/警告；`git diff --check` exit 0。使用相同隔离 jsonschema 命令对本次修改的两份 JSON、决定引用、7 项 accepted ADR、第二轮完整 frontier 与七份工件空白格式复验，exit 0；D-010 指向 LOG-029，round=2，frontier 精确为 D-020/021/022/023。四项问题已呈现，等待回答；原 source 摘要偏差仍保留，不把这次限定范围的通过当作来源偏差已修复。

**第二轮回答后的复验（2026-09-20）：** grill、triage、`git diff --check` 均 exit 0，两项 workflow 校验均为零错误/警告；同一隔离 jsonschema 命令检查两份 JSON、D-020～023 的 LOG 引用、10 项 accepted ADR、七份工件及第三轮完整 frontier，exit 0。已回读 LOG/ADR/CONTEXT/设计树。round=3，D-024～030 七项问题已呈现且保持 open；未将知识/文档/MCP 样例的回答扩展为部署或容量承诺。


**第三轮回答后的复验（2026-09-20）：** grill、triage、`git diff --check` 均 exit 0，两项 workflow 校验均为零错误/警告。同一隔离 jsonschema 命令检查两份 JSON Schema、20 节点无环依赖、43 条 LOG 与决定引用、14 项 accepted / 2 项 superseded ADR、七份源工件及第四轮完整 frontier，exit 0；source 与 HEAD 字节相同，既有声明摘要偏差未变。已回读 LOG/ADR/CONTEXT/设计树；round=4，D-031/032/033 三项问题已呈现，推荐均未接受。仅设计文档验证，未执行产品构建、模型调用、数据库迁移或故障恢复实验。

**独立复核修正：** ADR-001 的必需 WTA 接入验收已通过 ADR-013 建立局部替代关系，使用通用 HTTP 客户端验收；保留历史正文。修正后 grill 再验 exit 0、零错误/警告，`git diff --check` 与四工件回读/替代链/空白检查 exit 0。

**第四轮回答后的复验（2026-09-20）：** grill、triage 与 `git diff --check` 均 exit 0，两项 workflow 校验为零错误/警告；同一隔离 jsonschema 命令检查两份 JSON Schema、21 节点无环依赖、47 条 LOG 引用、17 项 accepted / 2 项 superseded ADR 及七份工件，exit 0。已回读四份权威工件；第五轮完整 frontier 仅 D-034，Q18 已呈现，未把可信标识传入或记忆写入默认值当作用户已确认。RAG、MCP 等运行能力尚未实测，本次为设计记录校验。

**第五轮收口复验（2026-09-20）：** grill、triage、`git diff --check` 均 exit 0，两项 workflow 校验为零错误/警告。隔离 jsonschema 命令检查两份 Schema、21 个全部 answered 节点、无环依赖、49 条 LOG、18 项 accepted / 2 项 superseded ADR、七份工件及空 frontier，exit 0；四份权威工件均已回读。source 与 HEAD 逐字节相同，原有声明摘要偏差仍保留。独立只读复核未发现新增高影响问题。技术运行及容量效果未验证；当前仅待用户对 LOG-049 确认整体共识，之后才记录 consensus 和交接 S，不自动执行下一 Work。

**G 完成验收（2026-09-20）：** 用户整体确认见 LOG-050。grill、triage、`git diff --check` 均 exit 0，两项 workflow 校验为零错误/警告；隔离 jsonschema 命令验证两份 Schema、21 个 answered 节点、consensus 与真实确认引用、无环依赖、50 条 LOG、18 项 accepted / 2 项 superseded ADR，以及 G 的 works_run/current_work 完成状态，exit 0。七份工件已回读且空白格式通过；triage 路由 specdev/spec，尚无 spec.md；change 与全局索引仍 active，实施授权保持原状。来源与 HEAD 字节相同，已知声明摘要偏差仍保留。G 阶段完成，产品实现与运行验收属于下游。
