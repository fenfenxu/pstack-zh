### Runtime forensics

**诊断由你负责。给运行中的进程插桩，不要对着源码推测。** 交付的是一份注明出处的诊断，不是修复。

1. 用 control skill 在对应的界面上采集运行时的信号：进程空转就抓 CPU profile，内存泄漏就抓堆快照，画面异常就抓 CDP trace。要的是真实产物，不是猜测。
2. 把产物缩减到确凿证据：热路径上的那个函数，从泄漏对象到 GC root 的引用链，没有输入也在不停触发的循环。大的产物交给子代理解析（**guard-the-context-window** 原则 skill），主对话里保留缩减后的 finding（查到的结论）。
3. 先证明机制，再相信它。用 CDP eval 往运行中的进程里注入插桩，或者不重新加载、直接热修正在运行的代码，低成本地确认假设。
4. 把 finding 对应回源码：文件、符号，以及做分配或调度的那一行。
5. 吞吐检查点只写一行：`throughput checkpoint: n/a, read-only forensics`。

**回复：** 采集到的信号、缩减后的 finding、你怎么证明的机制、源码位置、产物路径。没人要求就不修。查明原因之后，交回 Bug fix 或 Perf。
