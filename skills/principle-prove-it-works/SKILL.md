---
name: principle-prove-it-works
description: "完成任务后、声明 done 前适用。针对真实 artifact 验证（运行功能、读实际值、检查 diff），而非 proxy、自报或「能编译」。"
disable-model-invocation: true
---

# 证明它有效

通过直接检查真实对象来验证每个任务输出。不要从 proxy、自报或「能编译」推断。

**原因：** 未验证的工作正确性未知。间接验证（file mtime、output freshness、agent 自报、cached screenshot）感觉比直接观察便宜。基于错误推断行动的成本远高于检查源。

检查真实对象，不是 proxy：
- 直接检查 process liveness，不要通过 derived state 间接检查
- 读实际值，不是 cached 或 derived representation
- 验证失败时，先怀疑 observation method，再怀疑系统

## 能写成脚本的检查就写成脚本

最强证明是确定性脚本重跑同一比较，不是一次性 eyeball。写脚本、运行它，并把输出保留为审查者可重跑的 artifact，而非信任你的话。

让人类可见 artifact。仅在大型或复杂工作、且 trail 需日后可审计时提交，例如大 port 或 migration（**show-me-your-work** skill）。
