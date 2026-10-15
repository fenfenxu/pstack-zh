---
name: principle-guard-the-context-window
description: "当 context 即将填满时适用：大输出、长文件、重复读取、fan-out 规划。把 bulk 路由给子代理；主线程保留摘要，而非原始 payload。"
disable-model-invocation: true
---

# 守护 Context Window

**原因：** Context 溢出会降低推理质量、产生压缩 artifact，并阻断进度。

**模式：**
- **按阶段定规模并 cap scope。** 限制每阶段文件数、设定 turn budget、计入机制成本。
