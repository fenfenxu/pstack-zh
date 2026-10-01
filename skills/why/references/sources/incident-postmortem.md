# 事故与事后复盘上下文

非独立来源，而是**跨切面角度**。事故常驱动防御性代码（「X 故障 后我们加了此检查」），若目标看起来防御性（空值检查、重试、超时、限流、功能开关），应在每个可用来源中专门追查事故历史：

- **Notion**：搜提及目标文件、功能或错误串的事后复盘
- **Linear**：找标签 `incident`、`sev-*`、`postmortem-action-item`、`reliability` 的 ticket
- **Slack**：在目标代码添加日期附近搜 `#sev-*` 与 `#incident-*` 频道
- **Git**：提交信息含「fix for incident」「add defensive check」「revert」后跟「re-apply with...」是强信号
- **Datadog**：`search_datadog_incidents` 查正式事故记录、时间线、作为事后 action item 创建的仪表盘与 monitor
- **Sentry**：首次/末次出现窗口与目标 PR 发布日期对齐的 issues，堆栈经过目标
- **Databricks**：分类错误条件的产品分析 事件（客户端上报失败、用户可见重试 事件 等）常在事故窗口尖峰。该 event 计数在目标 PR 后下降，即使 Datadog/Sentry 信号噪声大，也是目标代码 解决用户可见症状的旁证。

若找到事故链接，拉完整事后复盘。复盘通常有「Action Items」节，直接关联代码变更。多来源 相互印证（Datadog 事故 ID 出现在 Linear 工单，出现在 Notion 复盘，出现在链到目标 PR 的 Slack 讨论串，且 Databricks 错误事件计数修复后下降）时证据尤其强。

当代码防御特征使事故驱动起源 说得通 时值得花时间。对看起来不防御的代码可跳过。
