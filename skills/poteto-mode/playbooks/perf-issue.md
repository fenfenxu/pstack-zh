### Perf issue

**你拥有测量叙事。规划、review、验证数字。** 每个 fix 绑测量，不要读源码代替测量。

1. 经匹配 control skill 捕获 baseline trace。
2. `how` grounding 假设。未跑过不要 claim perf ceiling。
   多数 fix 来自八类策略族。作 hypothesis 生成器，非 checklist。仅 trace 显示其命名信号时才值得尝试。
   - **Elimination.** 优化 hot path 前先问是否必须存在：无人消费的计算、对该用户永远 off 的 feature gate、冗余 mirror state 的 sync、因「以防万一」保留的 legacy path。trace 显示慢，从不说明可删，故需 `how` pass，非 profiler。
   - **Divide and conquer.**  dominant cost 随输入规模 scaling。split 使每块触更少（chunk、shard、prune search space）或独立块并行。
   - **Caching.** 相同输入重复相同计算或 fetch。存结果复用。claim win 前命名 invalidation。
   - **Indirection.** hot path 做 expensive work，更 cheap intermediate 可吸收：index 替代 scan、queue 把 work 移出交互线程、handle 允许 swap cheaper implementation。仅当从 critical path 移除多于新增 hop 时加 hop。
   - **Batching.** 许多小操作各付 fixed overhead（RPC、query、syscall、draw call）。合并为每 batch 付一次 overhead。
   - **Redundancy.** 等待挂在一慢 instance 或 attempt。duplicate work（replica、hedged request、speculative execution）取最快结果。trace 须显示 wait dominate 且系统有 headroom。
   - **Lazy evaluation.** cost 落在未用或尚不需要的结果（boot path eager init、渲染 offscreen item）。defer 到 first use。
   - **Scheduling.** work 必须发生，但不在交互时刻。移到无人等待处：idle callback、boot 后 background warmup、用户到达前 precompute、frame commit 后 cleanup。win 是感知 latency，故测交互 path，非总 work。
3. 从 trace 规划 fix。跨函数边界则先 `architect`。用配置的 perf-issue model（默认 `grok-4.7-xhigh-fast`）delegate 实现。review diff。捕获 post-fix trace。
   应用 **sequence-verifiable-units** principle skill，下一尝试前 verify 每次 attempt。
4. 解析并比较人工制品（JSON 到 sqlite、diff）。「Inconclusive」或 wrong-surface 不算 pass。标记。
5. PR 中 cite 测量。
6. 运行 **Opening a PR**。

相对 metric 的 sustained 改进而非一次性 fix 用 Hillclimb playbook（`playbooks/hillclimb.md`）。

**Reply：** baseline 数、post-fix 数、delta、artifact 路径。
