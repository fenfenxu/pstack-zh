### Trace forensics

**你负责从产物得出诊断。加载、塑形、收窄至原因、归因到源码。**

与 **Runtime forensics** 不同，后者对 live 进程做 instrumentation。此处 capture 已存在。产物是固定数据集：读取它，不要重跑。保持工具通用以便 playbook 可移植：cpuprofile 与 `.json.gz` 用 DevTools 或 trace 解析器，spindump 用文本编辑器，heapsnapshot 用你的 heap 工具。

1. 识别格式并用合适工具加载。在子代理中解析大型产物（**principle-guard-the-context-window** skill），将缩减结论保留在主线程。
2. 将原始产物转为可查询形式。将 trace 或 heap snapshot dump 到 sqlite，每 sample、frame 或 node 一行。阅读前先达到可查询形状。
3. 收窄至原因。查询占用最多时间的 frame 并沿 call tree 走到热路径。泄漏时沿 retainer 链从泄漏对象到 GC root。spindump 时找到持续占用 CPU 或阻塞的线程及其等待原因。
4. 归因到源码。通过产物自身 symbol 将热 frame 映射到 file、symbol、line。无 source mapping 的 frame 尚非诊断。解析 symbol，或明确说明产物未携带它们。
5. 有配对 capture 时对照确认。对比前后产物。没有时，将结论标为产物支持的最强假设，而非已确认原因。
6. 交回带引用的诊断，除非被要求否则不提供修复。原因明确后路由到 Bug fix 或 Perf issue。Throughput checkpoint 保持一行：`throughput checkpoint: n/a, read-only forensics`。

**回复：** 产物与格式、缩减结论、源码位置、产物 path，以及配对 capture 是否确认。
