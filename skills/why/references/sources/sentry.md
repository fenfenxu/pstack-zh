# Sentry 错误历史

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
