# 前端

本目录拥有独立 pnpm 工作区，计划构建两个 App：

- Console：超管配置、开发、发布与运营 AI 应用，管理资源和调用密钥，审批具体写工具动作。
- Developer Portal：通用接入指南、鉴权后的服务文档、API Reference 与受限试调。

已确认 Vue 3、TypeScript、Vite、Vue Router、Pinia、Element Plus，图编辑使用 Vue Flow，API Reference 使用 Scalar。两个 App 通过公开的管理/运行合同访问服务端，可共享本项目内部包。

当前尚未生成 App 源码、依赖清单或锁文件；构建与部署合同在实现时建立。设计来源见 [ADR-002/007/021](../speculo/.speculo/specdev/changes/2026-09-19-go-python-ai-platform/ADR.md)。
