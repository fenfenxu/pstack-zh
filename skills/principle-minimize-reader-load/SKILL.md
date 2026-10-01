---
name: principle-minimize-reader-load
description: "审查或塑造难以 trace 的代码时适用。统计问题与答案之间的层数，以及读者脑中 hidden state；collapse 单 caller wrapper，缩小 mutable scope。"
disable-model-invocation: true
---

# 最小化读者负担

可维护性是读者理解代码必须做的工作。追踪两个轴：
1. **需 trace 的层数。** 问题与答案之间有多少 indirection。
2. **需持有的 state。** 读者脑中需保持多少 hidden 或 mutable context。

**原因：** 代码读远多于写。LOC、cyclomatic complexity 和「clean architecture」都是 proxy。Reader load 才是要害。两轴独立。50 个 global 的 flat file 可能与 6 层 adapter stack 一样难推理。两者都要 guard。这是 [Guard the Context Window](../principle-guard-the-context-window/SKILL.md) 的人类类比。读者的工作记忆也有限。

**模式：**
- **Collapse 得不偿失的层：** 单 caller 的 wrapper、无第二实现的 adapter、从未需要的 speculative indirection。Inline 它们。
- **让相邻层改变抽象。** 重复相同方法和参数的层增加 reader load 却无 compression。Collapse pass-through 层。
- **要求 interface compression。** 隐藏很少复杂度的 broad interface 让读者既要学 surface 又要学实现。Prefer 隐藏 meaningful decision 的边界。
- **缩小 state scope：** 优先纯函数（return 而非 mutation）、local 优于 field、field 优于 module state、module state 优于 global。Derive 而非 sync。
- **在边界命名 invariant，** 不在每个 consumer 里，让读者只学一次。
- 加层或加 state 前问：是否在其他地方至少同等减少 reader load？

**检验：** 新读者能否在 30 秒内回答「X 从哪来？」和「什么能改变 X？」若不能，砍层或砍 state。
