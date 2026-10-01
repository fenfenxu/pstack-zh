---
name: principle-migrate-callers-then-delete-legacy-apis
description: "引入新内部 API 而旧 caller 仍存在时适用。在同一波次迁移 caller 并删除旧 API，而不是保留 compatibility layer。"
disable-model-invocation: true
---

# 先迁移 Caller 再删除 Legacy API

当我们认定新 API 是正确设计时，在同一 refactor 波次迁移 caller 并移除旧 API，而不是保留 compatibility layer。

**规则：**
- 不要仅因内部 caller 仍存在就保留 legacy API path
- 清点 caller，迁移它们，并立即删除旧 API
- 把临时 adapter 视为例外且 time-boxed，不是默认架构
- 更新测试以断言新契约，删除只保护 refactor 前实现细节的测试

**适用条件：**
- 无外部用户依赖 backward compatibility
- 项目能承受 coordinated breaking change
- 新 API 是 simplification 或 refactor  initiative 的一部分

同时保留新旧 API 会造成 dual-path 复杂度、拖慢 cleanup，并让代码库感觉像 append-only。
