# Sentry 错误历史

## 这个来源里有什么

Sentry 是出错之事的档案。对防御性、纠正性或错误处理代码，它常持有直接动机：促使某人加检查、catch、重试或 fallback 的具体异常、堆栈与频率。

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

   好查询成分：目标处理的异常类名、目标函数或类名、目标检查的错误消息字符串、目标文件路径。

3. **按 release 与时间窗口收窄。**

   ```
   search_issue_events (filter by release, time, environment, trace ID, tags)
   get_issue_tag_values (for an issue, see distribution across versions, users, environments)
   ```

   对疑似 issue 查：
   - **首次出现。** 错误何时开始？
   - **末次出现。** 何时停止？是否与目标发布日期对齐？
   - **受影响 releases。** 哪些版本见过？哪个是修复版？
   - **频率轨迹。** 是否尖峰后解决？

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

   Seer 产出 AI 根因分析。可作假设生成器，但视为推断非权威。实际 events 与堆栈是主要证据。Seer 叙述是次要。

## 这里怎样算好证据

- issue **首次出现** 在目标 PR 前不久首次出现、**末次出现**在之后不久，暗示目标处理了该错误
- 堆栈经过或落在目标函数，展示确切失败模式
- issue 上 PR 作者的评论描述修复
- 目标 PR 描述或提交信息引用 Sentry issue URL 或 ID
- 高事件计数的 issue 在含目标的 release 后停止

## 常见陷阱

- **分组漂移。** Sentry 按指纹分组。重构或改名可能把「同一」错误归到新 issue ID。issue 突然结束可能是重新分组。查之后立刻出现的新 issues。
- **Release 关联噪声。** 一个 release 含许多提交。issue 在 v2.14.0 停止不证明是目标修的。同 release 其他变更可能修了。与目标确切 commit 交叉引用。
- **静默修复。** 错误停止可能是因为前面的服务变了，不一定是这段防御代码修好的。有关联提示性，不能证明是这段代码修的。
- **Resolved ≠ fixed。** issue 可手动标 resolved 而无代码变更。把 `resolved` 当人工标记，非代码修复证据。
- **Seer 幻觉。** Seer 可生成听起来自信但不对的解释。主张时回退到实际 events、堆栈、时间戳。
- **采样。** 有些项目大量采样 events。低计数可能只是高采样，非罕见错误。有疑则记缺口。

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
