---
name: principle-fix-root-causes
description: "调试时适用。将每个症状追溯到根因并在那里修复；先复现，不断问为什么直到到达根因，抵制用 nil-check 守卫掩盖崩溃。"
disable-model-invocation: true
---

# 修复根因

调试时不要修症状。把每个问题追溯到根因并在那里修复。

**原因：** 症状修复会累积。每个 workaround 都让系统更难推理，而真正的 bug 仍在。根因修复前期更慢，但总调试时间更少。

**模式：**
- 先复现
- 不断问「为什么」直到到达根因
- 不要加守卫（加 nil check 掩盖崩溃是症状修复）
- 若 workaround 需要一段长注释才能自圆其说，代码就是错的（修代码，不是修注释）
- 查模式，不只查实例（grep 同一模式，修所有实例）
- 卡住时，加 instrumentation。不要猜（加 logging，读实际错误）

**重启类 bug：先怀疑 state，再怀疑 code**

当某物「重启后失败」时，先怀疑 stale persistent state：配置文件、缓存、锁文件、序列化 state。若清除 state 文件恢复行为，优先把 state 验证作为修复。
