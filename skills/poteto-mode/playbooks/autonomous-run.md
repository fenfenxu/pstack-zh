### Autonomous run

**你拥有退出条件。先定义 done，然后不停驱动至满足。**

1. 第一次迭代前，将退出条件陈述为可检查 predicate（测试绿、复现修复、全部 N 个 PR merged、pixel-diff 为零）。
2. 用 Cursor 的 `/loop` 命令（内置，非 pstack skill）选 wake 机制。要 watch 的事件（CI、merge、ref 前进）用 watcher 子 agent 在事件时唤醒你，并以长周期 time-based heartbeat 为 fallback。无事件则用固定间隔 heartbeat，大小取决于何时值得 re-check。
3. 每轮迭代做证据 justify 的最小变更，对 predicate 验证，前进则 commit，无效则 discard。belt-and-suspenders「可能有帮助」要 revert，不要留着碰运气。
   按 **sequence-verifiable-units** principle skill 排序工作，每单元下一步前验证，而非末尾批量检查。
4. 中途发现由你处理。通过 poteto-mode 自行处理损坏 skill、相关 bug、flaky verifier、review 噪音、工具失败、orphaned follow-up、可修 drift。带外 fix 放独立 PR。不要为可逆工作 park 给人类或使用 `AskQuestion`。仅 surface 不可逆动作、任何实验无法 settle 的真实产品/偏好选择，或真实 dead end。predicate 仍是主驱动，每次 side fix 后回到它。
5. 每轮通过 **show-me-your-work** skill checkpoint：一行记录变更与 predicate 是否移动。
6. predicate 满足时停止。plateau 不是停止理由，继续并 pivot 方法突破。surface 真实 dead end 而非空转，绝不 relax predicate 来宣布胜利。

**Reply：** 退出条件、迭代次数、落地内容、discard 内容、最终 predicate 状态。
