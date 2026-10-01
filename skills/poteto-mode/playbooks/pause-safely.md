### Pause safely

**你拥有干净停止。留下 cold-start agent 可恢复的 checkpoint。** 仅显式触发。对「keep going」「going to bed, keep going」「don't stop」不要 pause。

1. 在 safe boundary 停止。完成当前 atomic step 或 back out。不要 start 新事，cancel 嵌套子 agent。
2. 为 pause 不做不可逆动作。除非已有，否则无 PR、无 push。
3. 使 work durable。将未 commit 编辑作为清晰 `wip:` commit 于当前 branch，避免丢失。树 broken 则在 commit body 一行说明。
4. 在 context 外写 resume note。capture 意图、在做什么、progress 与已 verify 项、当前 state、next steps、关键文件、gotcha。compaction trigger 时写到如 `/tmp/<slug>-resume.md`。若存在 show-me-your-work trail，指向它而非 duplicate。

**Reply：** loop 中位置、磁盘上 vs 仍在脑中（路径，无 diff dump）、所做 commit 与树是否 clean、resume 时 first action。这是 pause，非 final report。
