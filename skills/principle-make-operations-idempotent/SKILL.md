---
name: principle-make-operations-idempotent
description: "设计在崩溃、重启和重试中运行的命令、生命周期步骤或处理循环时适用。无论先前部分运行如何，都收敛到相同 end state。"
disable-model-invocation: true
---

# 让操作幂等

设计操作，使其无论运行多少次、从何处开始，都收敛到正确 state。每个 mutating state 的操作都应回答：「运行两次会怎样？上次运行半途崩溃会怎样？」

**原因：** 命令、生命周期操作和处理循环运行在崩溃、重启和重试是常态的环境。若 partial state 改变下次运行结果，每次重启都会变成调试 session。

**模式：**
- Convergent startup：扫描已有 state，清理 stale artifact，接管 live session
- Content-based cleanup：按内容等价比较，不按创建顺序
- Self-healing locks：用基于 PID 的 stale lock 检测
- Idempotent scheduling：失败工作干净 respawn，每轮后 fresh input 重新生成

**检验：**
1. 连续运行两次会怎样？
2. 上次运行在每个可能点崩溃会怎样？
3. 重执行是否收敛到相同 end state？

若任一答案是「取决于留下了什么 state」，操作就需要 reconciliation step。
