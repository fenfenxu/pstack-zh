---
name: principle-separate-before-serializing-shared-state
description: "当并发 actor 可能写入同一文件、分支、键或状态对象时应用。先消除共享；仅当单一共享写入者是真实 invariant 时才在结构上串行化。"
disable-model-invocation: true
---

# 先分离，再串行化共享状态

当并发 actor 可能共享可变状态时，先问它们是否真的需要同一可变对象。若不需要，消除共享。共享真实时，在结构上强制串行化：lockfile、顺序阶段、独占所有权。指令与约定不是并发控制。

**原因：** 对共享状态的并发写入产生间歇性、难复现、调试昂贵的 race condition。

**模式：**
1. **识别共享可变状态**（双方读写的文件、双方 push 的分支、双方定义并消费的 API）。
2. **默认：消除共享写入目标。** 问：这些 actor 需要一份 canonical 对象，还是在发布独立事实？给每个 actor 自己的文件、键、分支或状态目录，仅在读/报告边界合并。两个 worker 往同一 `state.json` 写各自的 `lastX` 仍是共享变更。`indexer-state.json` + `metrics-state.json` 则不是。
3. **仅当单一共享写入目标是真实 invariant 时，在结构上串行化访问**（lockfile、顺序阶段、单写 actor 或原子 compare-and-swap）。把「我们需要锁」当作应检查的设计异味，不是默认答案。
