### Visual parity

**你负责像素级等价。基线是规格。不要动基线。** 等价由 image diff 验证，不靠肉眼。

1. 任何迁移前先建立基线：对当前组件各状态截图的视觉回归 harness，匹配两种实现时还包括目标侧。无基线则无从声称 parity。这是阻塞前提，不是后续补做。
2. 声明并遵守反捷径条款：不改 harness、不篡改基线、不为让 diff 通过而重组组件。若基线看起来不对，停止并询问，不要编辑它。
3. 一次迁移一个组件。跨 worktree 并行，每组件一个 owner（**separate-before-serializing-shared-state** 原则 skill）。共享 primitive 作为阻塞阶段优先迁移。
4. 通过 control skill 在匹配界面上用 image diff 对照各组件基线验证。非零 diff 即 fail。调查像素差异。每组件 `/loop` 直至 diff 为零。
5. 每组件或每个安全批次运行 **Opening a PR**。

**回复：** 已迁移组件、各组件 diff 结果、基线 harness 位置、剩余项。
