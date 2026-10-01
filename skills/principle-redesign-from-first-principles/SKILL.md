---
name: principle-redesign-from-first-principles
description: "将新需求集成进现有设计时适用。按该需求从第一天就是 foundational assumption 来 redesign，而不是 bolt-on。"
disable-model-invocation: true
---

# 从第一性原理 Redesign

集成变更时，不要 bolt-on 到现有设计。按该需求从一开始就在的方式 redesign。

- 读所有受影响文件并理解当前设计
- 问：「若我们从零写这个且带这个新需求，会建什么？」
- 把变更传播到每个引用：types、docs、examples、rationale section
- 先想完整 redesign，再增量交付

这是向现有设计集成变更时保留 option value 的方法。
