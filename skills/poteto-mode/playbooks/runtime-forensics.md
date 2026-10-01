### Runtime forensics

**你负责诊断。对运行中进程做插桩，不要仅从源码推测。** 交付物是带引用的诊断，不是修复。

1. 通过 control skill 在匹配界面上捕获运行时信号：空转进程的 CPU profile、泄漏的 heap snapshot、视觉故障的 CDP trace。要真实产物，不要猜测。
2. 将产物缩减到关键证据：热路径上的函数、泄漏对象到 GC root 的 retainer 链、无输入仍触发的 loop。在子代理中解析大型产物（**guard-the-context-window** 原则 skill），将缩减结论保留在主线程。
3. 相信前先证明机制。通过 CDP eval 向运行中进程注入 instrumentation，或不 reload 热修 live 代码，廉价确认假设。
4. 将结论映射回源码：file、symbol，以及分配或调度的行。
5. Throughput checkpoint 保持一行：`throughput checkpoint: n/a, read-only forensics`。

**回复：** 捕获的信号、缩减结论、如何证明机制、源码位置、产物 path。除非被要求，否则不提供 fix。原因明确后交回 Bug fix 或 Perf。
