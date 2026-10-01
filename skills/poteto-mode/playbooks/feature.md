### Feature

**你拥有设计。规划、审查、验证。** 将实现委派给子 agent，你保持主导。

1. 对受影响子系统运行 `how`。
2. 用 `architect` 做并行设计探索。
3. 将吞吐检查点写为四个 todo 项。确实不适用的维度（单文件、无扇出）仍保留该项，写 `n/a: <reason>` 而非删除：
   - **Blocking first steps.** 扇出前运行门禁。
   - **Independent workstreams.** 不相交的文件、服务或层可并行。共享写入须串行。
   - **Shared mutable state.** 默认拆分目标（**separate-before-serializing-shared-state** principle skill）。仅真实不变量时才串行。
   - **Smallest safe decomposition.** 若一个 worker 最合适，说明原因。
4. 用配置的 feature model（默认 `grok-4.7-xhigh-fast`）委派写代码，范围要具体（文件路径、命名数据形状及其组织方式——按 **principle-model-the-domain** 在委派写逻辑前选定，例如状态机替代散落布尔、表/registry 替代分支、typed model 替代重复形状假设——以及成功标准）。实现存在多种合理形状（错误处理、抽象层、测试结构）时，经 **arena** skill 委派，让 runner 呈现备选、交叉评审守卫选择。强制：不得以 skip-with-reason 逃逸，Laziness Protocol 不能覆盖（收益是审查分离，不是省行数）。禁止 spawn 的子 agent 须用同样审查分离直接拥有 diff。禁止「standing by」之类等待嵌套 agent 的回复。评论遵循 **Comments**。做外科手术式编辑，对由源文件生成出来的文件，重新对照源文件。共享 primitive 的改进须移植到所有 consumer 并分别验证。可频繁提交。
5. 在匹配表面验证。「Inconclusive」或错误表面不算通过。须标注。
6. 变基为小而有序的 commit。后续可堆栈。
   遵循 **sequence-verifiable-units** principle skill：构建、验证、提交每个小单元后再进行下一步。
7. 设计有争议时，交付前运行 `interrogate`。
8. 运行 **Opening a PR**。

代码耦合的工作（一个 feature 配一次 migration）交给单一 owner，检查点内联。该 owner 在阻塞阶段后内部扇出。父级扇出用于产出独立人工制品的切片（审计、跨子系统调查、竞争实验）。在阶段边界重写检查点。spawn 新 owner，不要依赖 interrupt 链。

**Reply：** 构建内容、选择与理由、吞吐检查点、未决项。设计备选用表。
