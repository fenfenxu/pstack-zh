# 事故与事后复盘上下文

- **Linear**：找标签 `incident`、`sev-*`、`postmortem-action-item`、`reliability` 的 ticket
- **Slack**：在目标代码添加日期附近搜 `#sev-*` 与 `#incident-*` 频道
- **Git**：提交信息含「fix for incident」「add defensive check」「revert」后跟「re-apply with...」是强信号
