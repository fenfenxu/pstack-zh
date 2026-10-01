---
name: principle-subtract-before-you-add
description: "编排 addition、refactor 或 rewrite 时适用。先移除 dead code、冗余 validator 和 stub reference，再在更简 base 上构建。"
disable-model-invocation: true
---

# 先减后加

演进系统时，先移除复杂度，再构建。

**原因：** 在复杂系统上加东西会 compound 复杂度。先移除留下更少代码、显露 essential structure，通常让下一设计显而易见。默认 subtraction。

把 simplification 当作持续投资。离开时设计应略更简单、能力相当或更强，surface 相同或更小。

**模式：**
- 构造前先 sequence removal
- 先 cut 再 polish（在投 quality 前先达到 minimum）
- 为 observed usage 设计，不为 speculative edge case
- 不要超出 spec 要求的 speculative validator、parser 或 guard
- 简化 prompt（删 redundant instruction、过多 template）
- 当 reference 无 novel content 时，删除而非留 stub
