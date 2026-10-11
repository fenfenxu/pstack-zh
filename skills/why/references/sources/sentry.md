# Sentry 错误历史

## 这个来源里有什么

对防御性、纠正性或错误处理代码，Sentry 常常握着直接动机：促使人加上检查、catch、重试或 fallback 的那些具体异常、堆栈和出现次数。

- **Issues。** 分组错误，含计数、首次/末次出现时间戳、受影响 release、评论
- **Events。** issue 内单个错误实例（堆栈、标签、用户上下文）
- **Releases。** 部署记录与关联 issues（「哪个版本修好了？」）
- **Replays。** 用户可见错误的会话录制（若启用）
- **Profiles。** 性能 profiling 数据（对「为什么」较少用，对「多慢」更多）
- **Issue 评论与指派。** 有时含工程师对根因的笔记

Sentry 最有价值的是**时间关联**：「issue X 创建于 2024-01-02，峰值 500 events/天，在 2024-01-15 发布 v2.14.0 后不再出现——该 release 含防御检查。」

## 怎么搜

使用 Sentry MCP。

1. **定向。** 若不知 project slug 与 organization：

   ```
   find_organizations
   find_projects
   ```

2. **搜与目标相关的 issues。**

   ```
   search_issues (natural language, e.g., "errors in PaymentService timeout", "unhandled exceptions in uploadFile")
   ```

   查询用这些来拼：目标代码处理的异常类名、它的函数或类名、它检查的错误消息字符串，以及它的文件路径。

3. **按 release 与时间窗口收窄。**

   ```
   search_issue_events (filter by release, time, environment, trace ID, tags)
   get_issue_tag_values (for an issue, see distribution across versions, users, environments)
   ```

   对疑似 issue，查**首次出现**和**末次出现**（停止的时间是否和目标的发布日期对齐）、**受影响的 release**（哪些版本见过，哪个是修复版），以及**频率轨迹**（是否先尖峰、然后被解决）。

4. **拉完整 event 上下文。**

   ```
   get_sentry_resource (pass a Sentry URL or type+ID)
   ```

   堆栈是否经过目标代码？标签与 breadcrumb 是否匹配目标防御条件？

5. **查目标附近发布的 releases。**

   ```
   find_releases (around the commit date of the target)
   ```

   将 release 版本与 PR 合并日期交叉引用。

6. **谨慎使用 Seer。**

   ```
   analyze_issue_with_seer
   ```

   Seer 的 AI 根因分析用来提出假设。把它当成推断，不要当成定论。真正的事件和堆栈才是主要证据。

## 这里怎样算好证据

- issue **首次出现** 在目标 PR 前不久首次出现、**末次出现**在之后不久，暗示目标处理了该错误
- 堆栈经过或落在目标函数，展示确切失败模式
- issue 上 PR 作者的评论描述修复
- 目标 PR 描述或提交信息引用 Sentry issue URL 或 ID
- 高事件计数的 issue 在含目标的 release 后停止

## 常见陷阱

- **分组漂移。** Sentry 按指纹给错误分组，所以重构或改名可能把「同一个」错误记到新的 issue ID 下。如果一个 issue 突然结束，错误可能只是被重新分组了。立刻去查后面新出现的 issue。
- **Release 关联有噪声。** 一个 release 里有很多提交，所以 issue 在 v2.14.0 停下来，不能证明是目标代码修好的。同一次 release 里的别的改动也可能修好了。和目标的确切 commit 对一下。
- **静默修复。** 错误停下来，可能是因为前面的服务变了，不是这段防御代码的缘故。这种对应提示了修复，但不能证明就是这段代码修的。
- **Resolved ≠ fixed。** issue 可手动标 resolved 而无代码变更。把 `resolved` 当人工标记，非代码修复证据。
- **Seer 幻觉。** Seer 可以听起来很有把握，却是错的。下结论时回到实际的 event、堆栈和时间戳。
- **采样。** 有的项目会大量采样 event，所以计数低并不等于这个错误罕见。拿不准就把这个缺口记下来。

## 要返回什么

每个相关 issue：
- Issue ID 与标题
- 项目与 organization
- 首次/末次出现时间戳
- Event 计数（及采样率若已知）
- 受影响 releases
- 展示与目标相关性的代表性堆栈片段（逐字摘录，非摘要）
- 首次/末次出现与目标发布日期的时间关联
- Issue 链接
- 作者评论或 解决说明
