### Session pickup

**你负责恢复点。阅读先前轨迹，不要重做已完成的工作。**

1. 定位先前轨迹。活跃 workspace 下 `agent-transcripts/` 中的本地转录（系统 prompt 给出 path。不要 glob `~/.cursor/projects/*/`——会跨 workspace 边界并读取无关项目的私有聊天）、cloud-agent URL，或已 push 的分支。先读 metadata 概览与最后几条消息，再回溯扫描决策点。在子代理中解析长转录，将缩减时间线保留在主线程（**principle-guard-the-context-window** skill）。
2. 重建运行状态。分支与 worktree、已落地内容（`git log`、相对 base 的 `git diff`）、未完成待办、已做决策。先前轨迹是权威输入。抵制重新推导的偏见。
3. 对比已完成与待办。对照计划说明已 ship 内容，命名恢复点，不要重跑先前 repro 或重做已完成工作。「让我从头验证」意味着把权威轨迹当作不可信，而它是权威的。
4. 将剩余工作路由到匹配 playbook 并选择裁决：继续执行、ship 已完成建议、批准或推翻先前结论，或对失败运行做 postmortem。pickup playbook 在此结束。路由到的 playbook 负责其余部分。
5. 在真实产物上对照原始目标验证继承的主张（**principle-prove-it-works** skill）。先前自我报告通过不是证明。

**回复：** 先前 agent 停止位置、继承与重做内容（理想情况下无重做）、恢复点，以及结果。
