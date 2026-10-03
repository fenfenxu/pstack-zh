### Investigation

**答案由你负责。先规划，再分派，然后写出来。**

调查类请求是只读的。产出的是带出处的解释或一条建议，不是代码改动。

1. 交给 **how** skill 处理。问的是动机，还要交给 **why** skill。
2. 吞吐检查点只写一行：`throughput checkpoint: n/a, read-only investigation`。
3. 产出 `how` 那种结构的结果（Overview / Key Concepts / How It Works / Where Things Live / Gotchas）。如果请求是在几个备选里做决定，就给一条建议，附一张取舍表。
4. 对回复用一遍 **unslop** skill。

不开 PR，不 babysit，不跑 `architect`，除非这次调查之后要改代码。如果要改，交还给用户，改走 Bug fix 或 Feature。

**回复：** 调查结果。回答「我们确定吗？」这类问题时，写出你真实的判断和理由。前提错了就直接反驳（见 Autonomy）。
