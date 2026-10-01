---
name: principle-guard-the-context-window
description: "当 context 即将填满时适用：大输出、长文件、重复读取、fan-out 规划。把 bulk 路由给子代理；主线程保留摘要，而非原始 payload。"
disable-model-invocation: true
---

# 守护 Context Window

Context window 在 session 内有限且不可再生。每个 token 都应值得其成本。

**原因：** Context 溢出会降低推理质量、产生压缩 artifact，并阻断进度。

**模式：**
- **隔离大 payload。** 把 verbose 输出、截图和大文档路由给子代理。主 context 得到摘要，不是原始数据。
- **把常用内容保持 inline。** 每次调用都用的模板和参考应放在 skill 文件里，不要放在每次都要 read 的单独文件中。
- **按阶段定规模并 cap scope。** 限制每阶段文件数、设定 turn budget、计入机制成本。
