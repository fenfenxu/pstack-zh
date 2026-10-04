### Perf issue

**整套测量由你负责。你来规划、评审，并核实数字。** 每个修复都要对应一次测量。不要拿读源码代替测量。

1. 用对应的 control skill 采集一份基线 trace（性能追踪记录）。用 **benchmark-checklist** skill 核查这份基线，之后得到的每个数字也一样要核查。
2. 用 `how` 给假设找依据。没实际跑过，不要断言性能已经到了上限。
   按下面的性能口诀依次试，先试代价最低的：
   1. 别做。结果没人用的工作，停掉，不要把它做便宜。
   2. 做，但不要再做一次。
   3. 少做。
   4. 晚点做。
   5. 趁别人没看的时候做。
   6. 并行做。
   7. 做得更便宜。

   前面的口诀已经达到目标，就停。
3. 根据 trace 规划修复。修复跨函数边界时，先用 `architect`。把实现交给子代理，用你配置的 perf-issue 模型（默认 `grok-4.7-xhigh-fast`）。评审 diff。采集一份修复后的 trace。
   按 **sequence-verifiable-units** 原则 skill 推进，每次尝试都先验证，再试下一个。
4. 解析并比较产物（把 JSON 导入 sqlite，做 diff）。结果是「Inconclusive」（没有定论），或者测错了界面，都不算通过。要把它标出来。
5. 在 PR 里引用这次测量。
6. 执行 **Opening a PR**。

如果要针对某个指标持续改进，而不是做一次性修复，用 Hillclimb playbook（`playbooks/hillclimb.md`）。

**回复：** 基线数字、修复后数字、差值、产物路径。
