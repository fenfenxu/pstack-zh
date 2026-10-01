# Source playbooks

why skill 为每个可用证据类别 spawn 一名 investigator，各读下方单一来源 playbook。这些 playbook 是常见 MCP 的具体示例。同一类别的不同 MCP 可据此改编。

| 类别 | Playbook | 示例 MCP |
|---|---|---|
| 源码历史 | [`code-archaeology.md`](./sources/code-archaeology.md) | git、`gh` |
| Issue / 工单跟踪 | [`linear.md`](./sources/linear.md) | Linear（可改编为 Jira、GitHub Issues、Plane、Shortcut） |
| 长文档 | [`notion.md`](./sources/notion.md) | Notion（可改编为 Confluence、Google Docs、Coda） |
| 实时团队聊天 | [`slack.md`](./sources/slack.md) | Slack（可改编为 Discord、Microsoft Teams、Mattermost） |
| 基础设施可观测性 | [`datadog.md`](./sources/datadog.md) | Datadog（可改编为 New Relic、Honeycomb、Grafana、Splunk） |
| 错误 / 异常跟踪 | [`sentry.md`](./sources/sentry.md) | Sentry（可改编为 Rollbar、Bugsnag、Airbrake） |
| 产品分析数仓 | [`databricks.md`](./sources/databricks.md) | Databricks SQL（可改编为 Snowflake、BigQuery、ClickHouse、dbt） |

跨切面：

- [`incident-postmortem.md`](./sources/incident-postmortem.md)。若目标代码看起来偏防御性（null check、retry、timeout、rate limit、feature flag、egress guard、OOM handler），则加入此项。
