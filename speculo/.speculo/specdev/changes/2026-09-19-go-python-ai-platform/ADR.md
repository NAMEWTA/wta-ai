# wta-ai 架构决定

本文件保存用户已明确接受的架构合同。调研、候选方案与取舍历史位于 <Path>{roots.state}/specdev/changes/2026-09-19-go-python-ai-platform/LOG.md</Path>；未确认的运行/权限/产品细节不表示 accepted。所有决定仅适用于当前 change，不表示实现已经完成。用户已在 LOG-050 确认整体设计共识，当前产品摘要见 LOG-049；项目名称、仓库、目录与发布所有权由 LOG-051 / ADR-021 更新。

当前采用超管登录、统一配置与密钥分发，无租户、无普通用户协作系统，与 WTA 仅网络交互；P0 包含完整 RAG、记忆与高级扩展。技术方案见 ADR-005～007；保留、部署、身份、预算、内容重放与 HTTP 合同见 ADR-011～016；完整 RAG、外部业务用户记忆隔离、MCP/Skill 管理及记忆信任/写入合同见 ADR-017～020。ADR-001 已被 ADR-021 替代，ADR-006 的工程作用域由 ADR-021 细化；ADR-004/010 已被 ADR-013 替代，以下旧文仅保留演进依据。框架补丁版本与运行能力仍需实施验证。

## ADR-001: 在 monorepo 内建设可独立运行的 AI 服务平台

**Status:** superseded
**Superseded by:** ADR-021 / LOG-051
**Source:** LOG-002 / 2026-09-20 user decision
**Supersedes:** none
**Verification refinement:** ADR-013 / LOG-038、LOG-039 替代下文必须验证 WTA 接入的子句；当前用通用 HTTP 客户端验收，WTA 仅为可选实例。独立运行要求继续有效；同仓目录决定后由 ADR-021 替代。

### Context
历史 Issue 提出 Go 网关和 Python 智能体，但未确定管理端归属。用户本轮明确语言可以调整，平台需要自己的 frontend 和 backend。

### Decision
平台根为 <Path>backend/wta-extend/wta-ai-server/</Path>，内部拥有 <Path>backend/wta-extend/wta-ai-server/frontend/</Path> 和 <Path>backend/wta-extend/wta-ai-server/backend/</Path>。平台可独立运行，WTA-plus 通过公开 HTTP 合同接入；目录处于 backend 下不决定其实现语言。

### Trade-off
相对于直接扩展 Java 管理应用，独立平台增加构建、运行与身份边界的维护成本，换取非 WTA 系统也可使用的平台能力及独立发布生命周期。

### Consequences
应建立独立 scope 和构建入口。数据库、审计和发布规则的适用边界需要专门裁决；本决定不自动授权 PostgreSQL 例外或修改永久工程规范。

### Verification / Migration
后续验证无 WTA 后端也可启动、配置并调用平台；验证经公开 API 的 WTA 接入。当前 AI Maven 占位与旧数据的处理必须另列合同，不以目录决定授权迁移。

## ADR-002: 管理控制台与开发者文档门户是两个前端访问面

**Status:** accepted
**Source:** LOG-003 / 2026-09-20 user decision
**Supersedes:** none

### Context
原设计重点是开发和运营控制台，本轮用户明确还需要专门供外部调用者阅读 Agent 服务 API 的前端文档平台。

### Decision
wta-ai-server 同时提供管理控制台与开发者文档门户。两者有各自的用户任务、导航及可见内容；文档门户围绕已发布服务提供调用说明。具体框架、是否两个构建 App、文档公开级别和在线试调权限仍进入设计树。

### Trade-off
相对于只暴露自动生成的 Swagger 页面，专门的门户增加前端维护成本，但能呈现发布版本、认证、异步运行、事件、错误及多语言集成过程。

### Consequences
文档也是交付合同，需要与运行 API 和服务发布一致；管理配置与敏感定义不能因生成文档泄露。

### Verification / Migration
外部开发者能够从门户找到指定服务、理解输入输出并按示例调用；控制台的管理能力拥有独立服务端授权。

## ADR-003: 同时提供运行 OpenAPI 与第三方管理 OpenAPI

**Status:** accepted
**Source:** LOG-004 / 2026-09-20 user decision
**Supersedes:** none

### Context
仅有运行 API 无法让 WTA-plus 或其他业务系统嵌入平台管理功能；依赖 Console 的私有请求又会形成不稳定集成。

### Decision
将外部调用已发布 AI 服务的运行 API，与外部管理平台资源和配置的管理 API，作为两个公开、版本化、可文档化的合同面。WTA-plus 后端可以消费管理 API 构建自己的管理集成。两者的授权范围必须分别定义，具体凭据协议待后续决策。

### Trade-off
相对于单一内部 Console API，公开管理面增加兼容、审计和权限测试成本，但第三方不需要依赖平台内部实现或操作平台数据库。

### Consequences
不能将运行接口文档充当全部管理接口合同；需要分别形成管理与运行 OpenAPI，并验证两者的权限边界。

### Verification / Migration
同一管理用例能够通过 Console 和授权的第三方客户端完成；仅持运行权限的调用者不能管理资源、发布服务或发行凭据。

## ADR-004: 平台自有用户身份，当前不建设租户体系

**Status:** superseded
**Superseded by:** ADR-013 / LOG-038
**Source:** LOG-023 / 2026-09-20 user decision
**Supersedes:** none

### Context
初始候选设计沿用了原稿的 Tenant/Project 模型，并以 WTA 适配描述部分集成步骤。用户随后明确平台与 WTA 完全独立，当前只需要独立用户，不需要租户。

### Decision
平台拥有自己的用户账户与认证授权体系。本期不建立 Tenant、tenant_id、租户管理或以 Workspace/组织命名的隐性租户。用户和资源之间的权限仍须服务端校验，具体角色及共享 ACL 另行冻结。WTA 与其他集成者地位相同，交互只通过公开网络协议，不依赖其登录、数据库、Java 模块或发布流程才能运行。

### Trade-off
相对于预建多租户 SaaS 内核，当前模型更符合实际范围，减少身份、迁移、配额和管理面的复杂度；将来若出现租户需求，需要独立评估身份/数据/凭据迁移，不能声称预留字段就已经支持。

### Consequences
原稿与第一轮候选中的 tenant 过滤、跨租户授权、RLS tenant 上下文和租户配额不进入当前产品合同。平台仍需要用户私有资源、显式共享、运行/文件授权和机器凭据治理。公共管理 API 能支持 WTA 集成，不要求本轮平台交付绑定 WTA 代码。

### Verification / Migration
在没有 WTA 的环境中完成账户、管理、发布和外部调用验收；验证用户 A 无权访问用户 B 的私有资源、运行和文件；验收中不得存在必填 tenant_id 或必须先创建租户的流程。

## ADR-005: Python 模块化后端以 LangGraph 为唯一图执行核心

**Status:** accepted
**Source:** LOG-029 / 2026-09-20 user decision；技术比较与提案见 LOG-025
**Supersedes:** none

### Context
原稿推荐 Temporal，第一轮质询后比较了通用持久工作流基础设施与 Agent 原生图编排。完整 P0 包含 Agent、Graph、Team、RAG、记忆与高级扩展；用户明确选择 LangGraph 技术方案。

### Decision
后端采用 Python 3.12、uv、FastAPI/Uvicorn、Pydantic 2、HTTPX。LangGraph Python OSS 是 Agent/Graph/Team 的唯一图执行与 checkpoint 权威，使用 PostgreSQL checkpointer（AsyncPostgresSaver），以 sync durability 为持久化基线。API、图执行 Worker、摄取 Worker 与分发器模块化组织、分进程运行。

Celery 5.6 系列 + Redis 8 只承担任务投递与运行唤醒，不用 Celery chain/chord 编排业务图，不以其 result 状态替代平台 Run 结果。平台负责持久接收/Outbox、崩溃重投、同一 Run 执行所有权、取消、预算与工具幂等。首版不引入 Temporal 或 Go 网关，也不要求 LangSmith 商业服务。

知识摄取选择 Docling；MCP 使用官方 Python SDK；Skill 采用平台版本化包与能力声明。平台 MemoryItem/Policy 拥有长期记忆的授权与生命周期，通过 LangGraph Store 接口适配；具体记忆政策与扩展执行信任仍由下游节点裁决。文件使用独立 S3 接口，身份采用平台用户/服务端会话与受限机器凭据。

### Trade-off
相对于 Temporal，LangGraph 更直接提供 Agent/状态图与记忆 primitives，并减少独立工作流服务的运维面；代价是平台必须补齐生产调度、互斥、恢复、命令消费和升级治理。选择开源库不等于已拥有托管 Agent Server 的全部能力。

### Consequences
Checkpoint 不保证外部写动作只发生一次。危险工具仍需稳定动作 ID、远端幂等/查询及 UNKNOWN 对账。图版本、运行公开合同与恢复实现由平台控制；不能因为有 PostgreSQL saver 就宣称进程故障可自动安全恢复。

### Verification / Migration
在 M0 固定兼容依赖与镜像摘要，验证重复投递、进程强杀、checkpoint 提交前后故障、旧 Worker 失权、重复审批、取消竞争及旧图恢复。并发/恢复机制的具体合同在 D-029 冻结；用户确认技术栈不代替这些实验。当前无旧平台运行引擎迁移，也不迁移 WTA 历史 AI 数据。

## ADR-006: 独立 AI 平台使用 PostgreSQL 与自有迁移，WTA 保持 MySQL

**Status:** accepted
**Source:** LOG-029 / 2026-09-20 user decision
**Supersedes:** none（对既有 MySQL 规则作当前 change 的显式 scope 裁决，不改写永久 ADR）
**Scope refinement:** ADR-021 / LOG-051 将下文旧 WTA 子目录 scope 改为独立 wta-ai 项目的 <Path>backend/</Path>；技术与数据库所有权继续有效。后续工程规范和 PostgreSQL 门禁在新项目建立，无需为独立平台修改 WTA 的数据库规则。

### Context
仓库 SEC-004 与永久 MySQL-only ADR 面向现有产品规定 MySQL 8.4 和父仓六份基座。独立 AI 平台使用 PostgreSQL checkpointer 与 pgvector，需要明确数据和迁移所有权，不能默认为已有支持。

### Decision
仅在 <Path>backend/wta-extend/wta-ai-server/</Path> 的独立平台 scope 使用 PostgreSQL 17、SQLAlchemy 2、psycopg 3、Alembic，以及 pgvector 0.8 系列。RAG 以向量检索与 PostgreSQL 文本检索/RRF 融合作为基线；实际检索质量必须实测。

平台拥有独立数据库、账号、配置和 Alembic 迁移；业务 schema 与框架 checkpoint/store schema 的迁移 owner 清楚分开。WTA Java 产品继续支持 MySQL 8.4，六份初始化基座的所有权和方言保持原合同；不共库、不复制 WTA SQL，也不以此决定宣称 Java 产品支持 PostgreSQL。平台不需要租户列或租户上下文。

### Trade-off
相对于统一 MySQL，增加一个数据库运维与恢复技术面，换取 LangGraph PostgreSQL 持久化及 pgvector 与平台数据的一致技术栈。需要独立的备份、升级、兼容与验收合同。

### Consequences
当前 change 已取得数据库作用域决定，不重复请求同一授权。实际实现前须同步工程 Skill 的项目画像、模块地图和数据库规则作用域，并建立真实 PostgreSQL/迁移门禁；这属于落实已接受决定，不意味着当前工作树已具备该能力。G 只记录 change-local 决策，不修改永久 ADR/context。

### Verification / Migration
独立初始化、增量升级、权限负向、备份恢复和回滚/前向修复测试必须执行。WTA MySQL 基座及既有发布回归仍按原范围验证。平台新库不自动导入或删除旧 AI 数据。

## ADR-007: 独立 Vue 工作区构建管理台与开发者门户

**Status:** accepted
**Source:** LOG-029 / 2026-09-20 user decision；补齐 ADR-002 的工程选择
**Supersedes:** none

### Context
两个访问面已经接受，但初始原稿 React 与当前仓库 Vue 能力存在选择空间。用户接受 LOG-025 的 Vue/Vue Flow/Scalar 技术方案。

### Decision
在平台自有 frontend 中使用独立 pnpm workspace，构建 console 与 developer-portal 两个 App。技术基线为 Vue 3、TypeScript 6、Vite 8、Vue Router、Pinia、Element Plus、Node.js 22 与 pnpm 10。图编辑采用 Vue Flow；文档门户由自有 Vue 页面与 Scalar Vue API Reference 组成。

图画布只编辑平台定义，不直接决定执行。门户消费管理/运行 OpenAPI 及发布服务输入输出合同；认证、目录过滤、试调与版本可见性由平台负责。两个 App 可共享平台内部公开包，不依赖 WTA App 私有代码或其运行时。

### Trade-off
相对于 React 重建另一套前端能力或只发布 Swagger，Vue 方案利用既有技术熟悉度，仍保留独立构建和部署；两个 App 增加产物维护成本，但各自导航、用户任务和公开范围清晰。

### Consequences
依赖通过平台锁文件固定，契约生成、图定义序列化和组件兼容需要门禁。公开目录、在线试调政策仍由 D-023 决定，技术选择不自动授权匿名访问。

### Verification / Migration
验证两个 App 独立构建/部署、Vue Flow 到平台 AST 的往返校验、Scalar/SDK 与 OpenAPI 3.1 的兼容、契约漂移、权限过滤和凭据不进入前端产物。当前没有需要迁移的已运行 AI 前端。

## ADR-008: 扩展使用配置出网与受控工具，Skill 不执行任意代码

**Status:** accepted
**Source:** LOG-031 / 2026-09-20 user decision
**Supersedes:** none

### Context
P0 包含完整 RAG、记忆与高级扩展，需要区分业务工具集成与用户任意代码执行。用户选择允许配置出网和受控写工具，未选择代码、终端及浏览器执行平台。

### Decision
模型、MCP、工具连接及出网范围由管理员显式配置。业务写工具必须经过服务端权限、审批和幂等控制；当前操作者或应用具有调用权限不自动意味着拥有审批权。审批角色、责任映射和业务参数政策在后续授权/集成合同中明确。

Skill 以指令、文件和能力声明组成版本化资源，可以通过平台引用已授权的受控工具；上传或导入 Skill 不会执行其中的代码、安装脚本或命令。P0 不提供用户任意代码、终端和通用浏览器执行器，也不能通过 MCP/Tool 的通用 shell/eval 接口绕过这一边界。

### Trade-off
相对于任意代码扩展，减少动态编程与自动化自由度，换取清楚的权限和副作用边界；仍可通过经登记的模型、MCP 及业务 HTTP 工具实现外部集成。未来若需要任意执行，需要单独设计与验收隔离能力。

### Consequences
平台需要连接/出网配置、工具风险分类、审批、幂等与调用审计的完整管理闭环。Skill 的能力声明是申请而非授权，指令不能修改平台政策。用户选择的是可配置能力范围，不表示已经批准某个生产供应商、地域、数据披露或真实业务写操作。

### Verification / Migration
验证未登记出口、无权工具和未批准写动作被拒绝；Skill 包中的脚本不被执行；重复写调用按业务幂等处理，结果不明进入对账而非盲目重发；撤销连接/授权后，Skill 或 MCP 不能绕过限制。具体测试依赖后续合同和实现，当前仅完成决定记录。

## ADR-009: 以不可变部署修订统一三种 Callable 的异步运行

**Status:** accepted
**Source:** LOG-033 / 2026-09-20 user decision
**Supersedes:** none

### Context
平台同时提供 Agent、Graph 与 Team，需要避免调用方依赖内部构图方式，也要隔离管理编辑与已发布服务的行为。

### Decision
Agent、Graph、Team 统一实现平台 Callable 合同。Deployment 是外部服务身份，指向不可变 DeploymentRevision；每个新 Run 固定自己使用的发布修订。外部通过统一 API 创建异步 Run，受理后返回 HTTP 202 与 run_id，使用查询、SSE 或 Webhook 获取执行进展和结果。

Graph/Team 必须有明确步骤、权限及预算上限。管理草稿和正常发布不能改变已经运行的修订，实时安全撤权仍可限制后续动作。外部协议不暴露 LangGraph 内部类或 checkpoint 作为业务入口。

### Trade-off
相对于同步直接调用可变 Agent 定义，增加发布修订、Run 查询和状态管理成本；换取生产行为可追溯、长任务可观察以及内部组合可演进。

### Consequences
契约、发布产物和门户展示需要版本一致；调用方持久化 run_id 才能断线后查询。具体幂等、流重放、错误、取消与恢复语义由后续合同补齐，不将“异步受理”表述为“业务完成”。

### Verification / Migration
三类 Callable 经同一个运行 API 完成；发布/回滚后在途 Run 仍使用原修订；202 后可以通过授权查询/事件获取结果；组合超过步骤/权限/预算上限被拒绝或受控终止。当前没有已上线调用者迁移。

## ADR-010: 通用指南公开，服务文档与试调按用户权限开放

**Status:** superseded
**Superseded by:** ADR-013 / LOG-038
**Source:** LOG-034 / 2026-09-20 user decision
**Supersedes:** none

### Context
开发者门户既需要降低外部接入门槛，又不能因文档发布泄露私有服务契约或提供共享生产执行权限。

### Decision
通用使用指南允许匿名访问。具体服务目录、版本及输入输出 Schema 默认需要登录与相应授权；在线试调使用受限开发凭据并执行平台权限、限额与审计。管理 API 文档只向具有管理集成权限的用户开放，文档可读不授予额外管理动作。

### Trade-off
相对于公开全部服务目录或匿名试调，增加认证接入步骤，换取私有服务可见性与成本的控制。公开指南仍可介绍协议、通用示例和申请权限的方法。

### Consequences
门户后端过滤目录与 Schema，不依赖 UI 隐藏；默认无匿名真实服务调用。浏览器不接收共享管理/生产 Key，试调凭据按开发用途限制；具体发行方式由接口合同冻结。

### Verification / Migration
验证匿名仅能访问通用指南，无权用户不可枚举私有服务或 Schema；已授权用户可按文档调用指定版本；无管理集成权限的用户不可读取管理专属文档，试调不得绕过执行授权与预算。

## ADR-011: 按类别配置保留策略，默认保留至主动删除

**Status:** accepted
**Source:** LOG-036 / 2026-09-20 user decision
**Supersedes:** none

### Context
用户拒绝统一设置运行/审计/记忆到期天数，要求内部平台默认长期保留。

### Decision
业务数据按类别配置保留政策，默认无自动到期，持续保留到主动删除；配置显式到期规则后才启用自动清理。运行正文、元数据、审计、记忆及持久内容事件适用同一默认原则。

### Trade-off
相对于默认短期清理，保留完整历史便于内部复盘，但存储、备份和删除传播成本持续增长。

### Consequences
记忆提取规则与留存期限分离。删除需要依赖检查、明确影响并传播到索引/缓存/内容流/对象等派生数据；活跃引用不能被静默破坏。备份与临时技术数据的生命周期另行规定，不能以此默认永久保留备份或凭据。

### Verification / Migration
验证默认无自动业务 TTL，显式配置的清理才生效；主动删除和恢复后删除传播可核对。容量不足告警/限流，不静默丢弃未到期历史。

## ADR-012: 以独立 Docker Compose 单机私有化交付 P0

**Status:** accepted
**Source:** LOG-037 / 2026-09-20 user decision
**Supersedes:** none

### Context
平台用于内部生产，用户选择先测量容量与恢复能力，而非首期多副本 HA。

### Decision
P0 以独立 Docker Compose 单机私有化为交付基线，平台及依赖不要求 WTA 服务存在。使用已选三类业务实测吞吐、延迟、长任务和恢复，依结果形成配置建议与适用范围。

### Trade-off
相对于多副本集群，降低安装维护成本；单机存在共同故障域，不能承诺高可用或未经验证的恢复指标。

### Consequences
数据卷、独立配置、备份/恢复和可复现版本属于交付内容。生产数值是实测输出；逐 token 持久化和默认持续保留计入磁盘与 I/O。

### Verification / Migration
记录机器规格、输入规模、模型服务与版本、并发、分位延迟、备份恢复耗时和数据损失边界；完成断电/进程失败等适当演练后才声明结果。

## ADR-013: 内部平台采用超管管理与密钥分发

**Status:** accepted
**Source:** LOG-038 / LOG-039 / 2026-09-20 user decision
**Supersedes:** ADR-004、ADR-010；ADR-001 中 WTA 接入作为必需验收的子句（局部）

### Context
此前以无租户多用户平台设计私有资源与共享 ACL；用户进一步明确只需超管登录、配置、分发密钥。

### Decision
平台独立管理认证仅面向超管；超管集中创建资源、发布服务、分发与撤销密钥。调用者无需普通用户账号。平台不建设租户、普通用户自助注册、用户私有资源/共享 ACL、角色组或专门集成应用身份产品。与 WTA 无登录、数据库或运行依赖。

密钥绑定明确的管理或运行用途；运行密钥只查看获准服务文档、调用服务和读取获准 Run，不能管理配置/发布/发行密钥。超管管理与两套公开 API 保留，管理用途凭据须显式发行；不要求先注册第三方集成应用。P0 暂不建设身份委托、外部审批同步或 WTA 专用适配。

保留一个管理控制台与独立开发者门户。通用指南公开；服务目录/Schema/试调按超管会话或受限调用凭据开放，无需门户终端用户注册登录。管理 API 文档仅向超管或获管理用途授权的凭据开放。

### Trade-off
相对于多用户资源协作，显著减少账户、角色、共享和审批责任模型；超管承担集中管理职责，调用密钥的分发/撤销与作用域仍必须正确。

### Consequences
超管能管理平台资源，不再承诺超管默认不可读用户私有内容。密钥持有人不能枚举其他凭据的 Run/文件/记忆。调用授权不等于外部业务写批准，ADR-008 仍生效。浏览器不内置共享管理 Key，门户使用持有人输入的受限调用凭据或受限会话。

### Verification / Migration
无 WTA、租户及普通用户账号即可完成超管登录、配置、发行密钥、查看服务文档与运行；通过通用 HTTP 客户端验证管理和运行 API，WTA 仅为可选实例。验证失效/撤销密钥、管理与运行用途混用、跨凭据 Run 访问和匿名服务调用被拒绝。原多用户/共享 ACL 及必需 WTA 接入验收替换为这些边界。

## ADR-014: 预算及发布质量检查作为强制门禁

**Status:** accepted
**Source:** LOG-040 / 2026-09-20 user decision
**Supersedes:** none

### Context
用户选择硬预算和固定评测/授权/恢复检查，而非仅提示成本及人工判断发布。

### Decision
预算不足拒绝新 Run 或阻止后续动作；已发生费用如实记录。发布必须通过固定数据集、Schema、授权与恢复检查；模型评分仅辅助，额度与阈值配置化并据实测确定。

### Trade-off
比仅告警更可控，但需要预算预留、并发结算及可复现评测，门禁失败会阻止发布。

### Consequences
平台/调用凭据/服务/Run 按实际政策限制，无租户或多用户配额前置。每次发起动作先原子校验和预留，完成后结算；不能撤销已在途费用或对未知成本上界作虚假承诺。

### Verification / Migration
覆盖并发抢占剩余额度、取消/超时费用、重复结算及评测失败阻止发布；记录数据集/版本、阈值与结果，不能仅依赖一次模型评分。

## ADR-015: 逐 token 内容采用持久事件日志并支持完整重放

**Status:** accepted
**Source:** LOG-041 / 2026-09-20 user decision
**Supersedes:** none

### Context
用户明确拒绝临时 token 流加最终快照，要求内容过程完整保存与回放。

### Decision
业务事件、最终结果和逐 token 内容增量持久化；同一 Run 支持从头及按游标重放，保持增量内容、顺序、节点/消息及 attempt 边界。内容增量在 PostgreSQL 可靠提交后才可对外发送，Redis 通知/缓存和 LangGraph checkpoint 均不能替代此日志。

重放仅读取持久日志，不调用模型或工具；客户端断线不取消 Run，取消不撤销已发生副作用，未知写结果进入对账。失败后重新生成属于新 attempt，不能作为丢失 token 的补写或与旧 attempt 混合。

### Trade-off
相对于短暂流，增加写入延迟、I/O、存储及恢复复杂度；换取断线重放和历史过程可核对。批量提交可以优化成本，但必须保留增量边界，提交前不发送。

### Consequences
持久化默认保留至主动删除；重放按当前凭据与事件可见性鉴权，不能暴露内部敏感数据。完整性覆盖平台可靠接收并确认的内容，不虚构供应商尚未交付或提交前失去的数据；中断如实标记。单机磁盘损毁仍受实际备份恢复边界限制。

### Verification / Migration
验证先提交后发送、序号去重、并行子图、多次执行分段、从头/断点回放一致、历史转实时无丢失、撤权/删除及备份恢复。LOG-041 给出事实来源和方案推导，当前没有运行测试证据。

## ADR-016: 管理与运行使用同源生成的版本化 HTTP 合同

**Status:** accepted
**Source:** LOG-042 / 2026-09-20 user decision
**Supersedes:** none

### Context
用户接受两套 v1 API 和统一传输/幂等/并发约定，避免外部集成依赖控制台私有请求。

### Decision
管理与运行分别发布 v1 OpenAPI；只读查询用 GET，状态变更用 POST；统一错误结构。创建 Run 使用 Idempotency-Key，编辑/发布校验版本冲突。SDK、门户与实现校验消费同一合同来源。

### Trade-off
相对于内部接口自由演进，增加兼容及生成漂移检查成本；提供稳定、可测试的外部调用面。

### Consequences
身份和资源集合以 ADR-013 为准，不接受旧提案的租户/projects/members/roles/service-principals。幂等隔离到可信调用凭据作用域与 Deployment，同键不同规范请求拒绝；重试重放结果前重新鉴权。POST 使用独立 Python 平台的安全审计机制，Java @Log 及 WTA 原有合同不因此改变。

### Verification / Migration
验证方法与业务语义、错误结构、同键重试/异参冲突、版本竞争、失效密钥和文档/SDK 漂移。公共流合同覆盖 ADR-015 的完整重放，幂等记录清理不能悄悄重新执行已受理业务。

## ADR-017: P0 以文件与受控 URL 交付完整 RAG 闭环

**Status:** accepted
**Source:** LOG-044 / 2026-09-20 user decision
**Supersedes:** none

### Context
完整 RAG 已进入 P0，但接入范围和知识处理闭环尚未冻结。用户接受文件与受控 URL 接入，不要求首期企业网盘或业务数据库同步连接器。

### Decision
P0 支持 PDF（含扫描 OCR）、DOCX、XLSX、PPTX、TXT/Markdown、HTML、CSV 上传及配置允许的 URL 接入；完整提供解析/切分预览、混合检索与 rerank、来源引用、增量更新/索引重建和删除。以中文、扫描件及表格样本验收效果。企业网盘与业务数据库同步连接器暂不进入 P0。

### Trade-off
相对于只提供向量检索，增加文档处理、OCR、版本更新、引用与删除管理的交付成本；相对于同时覆盖企业数据源同步，将首期工作聚焦在明确格式和可复现的知识处理闭环。

### Consequences
沿用 Docling、PostgreSQL/pgvector 及已接受的检索技术基线；格式与 OCR 能力以平台真实样本验证，不把组件声明当作实现证据。超管配置知识资源和出口，调用受服务/密钥授权与预算约束；文档内容不授予额外工具或网络能力。数据默认保留至主动删除，删除需覆盖派生内容并校验活跃引用。

### Verification / Migration
验证各格式及扫描 OCR 的解析/切分预览、检索与 rerank、引用回溯、增量更新、重建和删除；覆盖坏文件、处理失败、未授权 URL/资源、撤权和派生数据删除。使用中文、扫描件、表格样本建立固定评测集，记录检索/引用指标及阈值，纳入发布质量门禁。当前只确认设计范围，尚无解析、检索质量或容量实测结果。

## ADR-018: 记忆隔离包含外部业务用户标识

**Status:** accepted
**Source:** LOG-045 / 2026-09-20 user decision
**Supersedes:** none
**Refined by:** ADR-020 / LOG-048 已关闭下文 D-034 的标识来源、完整作用域与写入政策待定项；业务用户隔离决定仍有效。

### Context
平台只有超管账户与分发的调用密钥；单个业务系统的密钥可能服务多个终端业务用户。仅按调用凭据与应用隔离，会使这些用户进入同一长期记忆范围。

### Decision
短期和长期记忆均需按外部业务用户标识隔离，不能让同凭据下的不同业务用户默认共享记忆。此标识属于外部系统业务上下文，不在平台创建普通用户、租户或第三方登录账户；同名外部标识不能被当作跨调用范围的全局身份。

### Trade-off
增加调用上下文与记忆索引的维度，要求调用方提供一致的业务主体映射；换取无需建设平台终端用户体系也能区分业务用户记忆的能力。

### Consequences
会话归属、记忆读写/检索以及查看/纠正/导出/删除必须保持同一隔离边界。具体可信传入方、完整作用域、缺失标识行为与自动提取/人工确认政策仍由 D-034 确认，不能仅凭本决定自动信任请求中的任意用户 ID。数据默认保留至主动删除沿用 ADR-011。

### Verification / Migration
后续覆盖同密钥不同业务用户、不同调用范围的同名业务用户，以及跨主体会话访问/记忆检索/修改等负向用例；身份来源协议确认后再验证伪造和缺失标识。当前无已运行记忆数据需要迁移。

## ADR-019: MCP 与 Skill 由超管集中管理，写动作在本地审批

**Status:** accepted
**Source:** LOG-046 / 2026-09-20 user decision；细化 ADR-008
**Supersedes:** none

### Context
用户已经接受受控出网、写工具治理和非代码 Skill，进一步选择远程 MCP 与超管本地审批，排除本地命令执行及外部审批系统对接。

### Decision
超管配置远程 Streamable HTTP MCP，发现、审核工具后开放使用；首期不接用户提供的 stdio 命令。Skill 由超管导入版本包，校验文件/能力，绑定发布版本并支持停用、撤销，不执行任意代码。受控写动作由超管在管理台批准具体参数后执行，不接第三方审批系统，不建设复杂审批角色体系。

### Trade-off
相对于任意本地 MCP 或自动预授权，减少接入方式和无人值守写操作的灵活性；换取明确的外部连接范围、包版本及业务写批准责任。

### Consequences
工具发现、包导入和模型选择不等于授权。批准必须绑定具体动作/参数，参数变更不能复用旧批准；重复审批与恢复重试仍受动作幂等和 UNKNOWN 对账约束。发布固定 MCP 能力/Skill 版本仍须服从实时停用撤销。未来如需写工具预授权或本地 MCP，另行评估执行边界。

### Verification / Migration
验证未配置连接、未审核工具、未批准写动作及撤销资源不能调用；工具或参数变更需重新校验批准；Skill 包版本与发布一致，包内脚本不执行。覆盖重复审批、超管拒绝、等待批准期间取消和连接故障，不能用审批记录代替业务结果确认。

## ADR-020: 可信业务后端声明记忆主体，超管配置记忆写入

**Status:** accepted
**Source:** LOG-048 / 2026-09-20 user decision；补齐 ADR-018
**Supersedes:** none

### Context
外部业务用户维度已确认；用户进一步接受由可信业务后端验证自身用户并传入标识，避免在平台建设普通用户账户或身份联邦，同时明确记忆提取默认行为。

### Decision
可信业务后端持调用密钥，验证其业务用户后提交 external_user_id。长期记忆按调用凭据授权范围、应用、业务用户隔离；短期记忆再加入会话。需要用户记忆时业务标识必填，缺失不得回退共享记忆。此类可声明业务用户的服务密钥不分发到终端用户浏览器，平台不创建对应普通用户账户。

记忆自动提取默认关闭；超管按应用开启后依配置规则自动生效，也可改为人工确认。提供查看、纠正、导出和删除，不默认跨应用共享；保留策略沿用分类可配置、默认至主动删除。

### Trade-off
平台无需复制外部系统的用户与登录体系，但信任持服务密钥的业务后端声明其范围内的用户。调用方必须保护密钥并正确映射用户，平台不能仅凭该标识证明终端用户已经登录。

### Consequences
授权范围从已验证凭据取得，不能由请求覆盖；会话与记忆读写、查询、纠正/删除使用同一完整作用域。秘密轮换须保持或显式变更其记忆授权范围，不能因更换密钥值自动合并不同业务主体。门户受限试调不应获得任意声明真实业务用户的能力；测试主体及访问范围必须受其开发凭据约束，不能把后端服务密钥嵌入浏览器。平台超管的管理与审计权限仍按 ADR-013 执行。

### Verification / Migration
验证同凭据/应用下不同业务用户、不同调用范围同名用户、不同会话的隔离；用户记忆缺少标识时拒绝而不读取共享数据；撤销密钥、越界会话及试调凭据不能绕过检查。验证自动提取默认关闭、开启后生效、人工确认前不可检索、纠正/删除后派生索引不返回旧记忆，以及显式导出与保留配置。持可信服务密钥在其范围内声明用户是约定信任能力，不将它误报为平台具备外部终端身份验证。

## ADR-021: wta-ai 成为拥有完整交付资产的独立公开项目

**Status:** accepted
**Source:** LOG-051 / 2026-09-20 user decision
**Supersedes:** ADR-001；细化 ADR-006 的工程作用域与 ADR-012 的发布所有权

### Context
平台已有独立身份、数据库、双前端与双公开 API。用户进一步明确它是完整独立 AI Agent 平台，应从 WTA-plus 的后端目录迁出，拥有自己的项目、公开仓库和交付生命周期。

### Decision
项目名称为 wta-ai，公开仓库为 <Url>https://github.com/NAMEWTA/wta-ai</Url>。项目根直接拥有 <Path>frontend/</Path>、<Path>backend/</Path>、<Path>release-artifacts/</Path>、<Path>scripts/</Path>；前端仍包含 Console 与 Developer Portal，后端仍采用 ADR-005～006 技术栈。发布配置、镜像/静态产物合同及部署脚本由新项目自己维护，Docker Compose 单机私有化基线继续有效。

初始化本项目 Speculo，将本 change 完整迁入同名状态目录；保留 change ID、原始来源和历史决定，后续在本仓继续 S-spec。关闭 WTA-plus Issue #3，并说明其需求转由 wta-ai 继续推进。

### Trade-off
独立仓库增加版本与发布协调工作，但产品、依赖、规范、数据库、构建及部署的所有权与实际平台边界一致。

### Consequences
WTA-plus 只作为可能的公开 API 消费者；平台不依赖其源码目录、Maven、前端私有包、六份 MySQL 基座或发布清单。新项目自行建立工程规范与运行质量门禁。当前迁移只建立工程目录说明、初始化工具并承接已确认设计；源 change 没有前后端实现，不能将初始化报告为平台已可运行。

旧 issue 关闭表示工作转出，不表示产品完成；change 保持 active、设计 consensus、下一 Work 为 S-spec，ready_for_implementation=false。本次初始化与首次发布授权不扩展为后续产品实现、部署或任意提交授权。

### Verification / Migration
核对迁移前 8 份文件清单及 SHA-256，冻结 source 保持逐字节一致；检查新旧活动索引、Speculo doctor、SpecDev schema/triage/grill 和路径。远程验证仓库 public、main 已发布初始化内容、原 issue 已关闭且有新仓链接。历史引用的来源归属见迁移证据；不复制 WTA 业务代码、数据库或运行秘密。
