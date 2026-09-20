# 独立项目迁移证据

## 授权与完成标准

2026-09-20 用户明确授权建立 wta-ai 独立项目与 public 仓库、初始化 Speculo、迁入本 change、说明新项目去向并关闭 WTA-plus Issue #3。首次发布只包含初始化资产、设计记录与目录说明，不是产品实现提交或部署授权。

迁移前 8 份文件及各自 SHA-256 见 <Path>{roots.state}/specdev/changes/2026-09-19-go-python-ai-platform/evidence/project-migration.json</Path>。移动后、修订前逐项校验一致；source 继续保持原始字节。其声明摘要的既有偏差见 LOG-028，未通过迁移修复或隐藏。

## 历史引用归属

LOG-001～050、ADR-001 及 ADR-006 被细化的旧 scope、冻结 source 保留原始来源语境。其非当前 change 的旧项目路径属于 WTA-plus，不以新仓根解析，也不属于新项目构建依赖。

- 原仓：<Url>https://github.com/NAMEWTA/WTA-plus</Url>；LOG 记录的旧核对 SHA 保持不变，可用原仓历史定位。
- 旧临时设计输入 temp/wta-ai-platform-design 保留在原 WTA-plus 工作区；这些输入未受 Git 跟踪，不属于迁入工件，也不是新项目规范。有效设计和研究摘要均已随 LOG/ADR/CONTEXT 迁入；后续开发不要求访问旧临时原稿。
- 旧工程 Skill、Maven、MySQL 基座及 release 清单属于原仓，不能用于裁决新平台工程或部署。
- source 的原 issue-3 快照已随旧 change 归档，公开历史定位：<Url>https://github.com/NAMEWTA/WTA-plus/blob/main/speculo/.speculo/specdev/archive/2026-09/2026-09-19-remote-issues-phone-ai/sources/issue-3.md</Url>。本 change 的 source 已包含原始完整捕获内容，无需复制旧 change。
- 当前源码与交付目录以 ADR-021 为准；当前状态与 workflow 根均由本项目 workspace 解析。

## 初始化事实

- Speculo CLI 1.0.15；首次命令因父目录权限 EACCES 退出 1。
- 为当前用户建立目标目录后重跑，exit 0：781 managed files，安装所提供的全部 workflow packages。
- 独立 Git 默认分支 main；中文交互和工件语言、并发及 UI 偏好沿用已有明确配置。
- 产品 test/typecheck/lint/build 命令为 null：目前没有产品源码、依赖清单或构建脚本，不能复制 WTA Java/Vue 命令冒充新项目门禁。
- SpecDev 初始化配置和追踪约定来自已安装模板，空的永久知识目录就位；不提升设计为已实现永久知识。

## 本轮验证与远程结果

从新项目根运行，除标注旧项目的检查外：

| 命令 / 检查 | 退出码 | 结果 |
| --- | --- | --- |
| speculo doctor . | 0 | 安装与运行时根有效 |
| node validate-specdev.mjs --self-check | 0 | 工作流包 0 error / 0 warning |
| 同工具 --stage grill --repo . 当前 change | 0 | 0 error / 0 warning |
| 同工具 --stage triage --repo . 当前 change | 0 | 0 error / 0 warning |
| uv run --isolated --no-project --with jsonschema==4.26.0 python，内联检查 | 0 | 新 config/status/change-status/design-tree 与旧 status 共 5 份 schema；21 answered 节点、51 LOG 引用、无环依赖、18 accepted / 3 superseded ADR |
| python 内联文件、索引、SHA-256、公开内容及手写文件空白检查 | 0 | 8 份工件完整，source 字节未变，change 仅新项目 active，常见秘密模式零命中 |
| git diff --cached --check，全量初始导入 | 2 | 235 份 CLI 自带 workflow 文件存在原有空白格式提示，均位于受 CLI 管理的工作流资产；保持原始安装字节，不修改生成资产或放宽规则 |
| git diff --cached --check，限定本轮项目说明/状态与 change | 0 | 本轮手写与迁入设计工件无空白格式错误 |
| 旧项目 git diff --check | 0 | 无空白格式错误 |
| 旧项目 node validate-skill-facts.mjs | 0 | 5 canonical Skills、76 Markdown references 通过 |
| 旧项目 validate-specdev.mjs --capture 捕获账本 | 0 | 0 error / 0 warning |
| 独立只读迁移审查 | 完成 | 当前决定、历史保留、来源、索引和公开内容未发现阻塞问题 |

工具完整定位：<Path>{roots.workflows}/specdev/common/tools/validate-specdev.mjs</Path>；输入 change 为 <Path>{roots.state}/specdev/changes/2026-09-19-go-python-ai-platform/</Path>。旧项目 Skill 校验工具位于其 engineering-standards/scripts 下。完整全量空白输出仅保留本机临时审查日志，没有将外部模板全文复制进证据。

远程仓库创建、首次推送及 issue 关闭结果待执行后回填。

## 后续边界

change 保持 active，G consensus，下一 Work 为 S-spec；迁移与 issue 关闭不代表产品交付。未执行产品构建、运行测试、数据库迁移或部署。
