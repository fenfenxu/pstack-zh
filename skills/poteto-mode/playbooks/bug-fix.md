### Bug fix

**你拥有本任务。规划、审查、验证。** 将调查与修复委派给子 agent，你保持主导。

要科学。每条交付的代码行都可追溯到运行时证据。「可能有帮助」的冗余防护只是假设，不是修复，不能交付。证据反驳假设时，撤销它推动的改动。只交付证据所支持的最小变更。

1. 经 control skill（Non-negotiables）在匹配表面自行复现，即使 debug 或 instrumentation 协议要求用户复现。仅当 control surface 无法触达目标且有明确、具体理由时询问用户，且须先把 control 推到极限。若不能直接复现，则合成触发条件、收紧条件或加 instrumentation 直到触发。
2. 二分搜索原因。形成候选假设，逐一排除直到剩一个。用 `how` 扫受影响子系统，用 **why** skill 查回归历史。每轮选切分剩余问题空间最多的 split，取运行时证据，排除。程序状态不清时加 instrumentation 或 logging 并在运行时读取。不要猜。对长或顽固 hunt 用 Cursor `/loop`。在第 3 步 architect/interrogate 扇出前，用运行时证据确认幸存 *mechanism*。
3. 规划修复。若跨函数边界，先 `architect`。用配置的 bug-fix model（默认 `grok-4.7-xhigh-fast`）委派实现，范围具体。
4. 在同一表面验证。原复现现应通过。「Inconclusive」或错误表面不算通过。须标注。单元测试展示分支行为，不能证明 bug 不存在。
5.  staging commit 使失败复现落在 fix 之前 git 历史中。bug 有廉价本地测试路径时见 **tdd** skill 的 failing-test-first cadence。测试昂贵、integration-heavy 或 unclear 时跳过。
   这是 canonical **sequence-verifiable-units** principle skill：先 failing test，fix 在上。
6. 运行 **Opening a PR**。

**Reply：** 什么坏了、根因、修复、如何验证。逐字粘贴 failing-then-passing 复现输出。
