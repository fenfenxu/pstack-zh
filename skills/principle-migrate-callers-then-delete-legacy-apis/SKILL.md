---
name: principle-migrate-callers-then-delete-legacy-apis
description: "引入新内部 API 而旧 caller 仍存在时适用。在同一波次迁移 caller 并删除旧 API，而不是保留 compatibility layer。"
disable-model-invocation: true
---

# 先迁移 Caller 再删除 Legacy API

- 更新测试以断言新契约，删除只保护 refactor 前实现细节的测试

**适用条件：**
- 无外部用户依赖 backward compatibility
- 项目能承受 coordinated breaking change
- 新 API 是 simplification 或 refactor  initiative 的一部分
