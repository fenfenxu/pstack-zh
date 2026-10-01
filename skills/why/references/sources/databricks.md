# Databricks 分析与系统表

## 这个来源里有什么

Databricks 是产品分析、数据管道与数仓遥测层。与 Datadog 互补：Datadog 是*基础设施/运行时*视角，Databricks 是*产品/数据*视角（用户做了什么、哪些实验在跑、功能使用如何演变、阈值常量从哪来）。

- **产品分析事件。** `your_warehouse.events.analytics_track_event`（原始）及 `<your_analytics_db>.<schema>.<table>` 中 typed、dedup 的 per-event dbt 模型。用户行为：功能调用、点击、接受/拒绝、提交、客户端上报错误。
- **使用与计费事件。** `your_warehouse.events.usage_event` / `<your_analytics_db>.<schema>.stg_usage_events`，`your_warehouse.events.raw_model_event` / `<your_analytics_db>.<schema>.stg_raw_model_events`。成本或 流量驱动决策时用。
- **实验 / 功能开关数据。** 曝光与结果表。**Schema 因公司而异。** 假设名前用 `SHOW TABLES` 探测。
- **系统表。** `system.query.history`、`system.compute.warehouses`、`system.billing.*`、`system.access.audit`。回答「这查询贵吗？」「有人跑过几次？」「数仓负载何时尖峰？」
- **dbt 血缘。** `<your_analytics_db>.<schema>` 模型揭示哪些 pipeline 依赖某表/字段。数据来源一变，使用这些表的代码常常也要改。
- **Databricks notebook。** 工程师改代码前写的探索分析。**SQL MCP 不可查。** 若怀疑理由在 notebook，记为缺口。

## 怎么搜

使用 Databricks SQL MCP。主工具：`execute_sql_read_only`。若返回 `statement_id`，用 `poll_sql_result` 轮询，不要重跑。

**查询前先定向。** Schema 因公司而异。信任表名前探测：

```sql
SHOW TABLES IN <your_analytics_db>.<schema> LIKE '*<keyword>*';
DESCRIBE TABLE <your_analytics_db>.<schema>.stg_<event>;
```

**每条查询都限定时间。** 表巨大，无约束扫描 超时。在 `_timestamp`（events）或 `start_time`（`system.query.history`）上 filter，窗口 框住发布日期，通常前后约 30 天，有强理由才更宽。

**优先 typed dbt 模型而非 raw 表。** `<your_analytics_db>.<schema>.<table>` 去重、带类型、liquid-clustered。`your_warehouse.events.analytics_track_event` 有 重复与无类型 `properties_json`。模型名模式：`stg_<source>_<event_name_with_underscores>`，`<source>` 为 `app`、`backend`、`website` 或 `cli`。仅模式不够时用 `SHOW TABLES` 确认 确切模型名。仅当尚无 dbt 模型或需 dbt 刷新滞后内的 events 时才用 raw 表。

**typed dbt 模型列约定**（知道这些可省 `DESCRIBE` 往返）：

- `_timestamp`、`_id`、`_auth_id`、`_request_id`、`event_name`。每个模型标准字段
- `properties_<name>`。带类型、下划线化的事件属性（`properties_entrypoint`、`properties_size_bytes`、…）
- `context_team_id`、`context_client_version`、`context_country`、`context_client_os`。预提取客户端上下文

### 常值得做的调查模式

选与目标匹配的表 + 列组合：

1. **事件使用轨迹。** 相关 `stg_*` 模型在 PR 合并前后 ±30 天 的日计数。合并后一两天内从零到稳态流量的阶跃变化 是 PR 发布功能的强旁证。衰减到零暗示 弃用 或删除。
2. **护栏/防御检查起源。** PR 前 14 天相关 `properties_<name>` 列分布（median / p99 / max）。p99 匹配目标阈值常量暗示数字来自数据。
3. **实验/功能开关查找。** `SHOW TABLES ... LIKE '*experiment*'` 找曝光表，再按相关 flag key 在 PR 日期附近拉 variant 曝光计数。
4. **迁移、回填 或 性能重写的 query-history 证据。** `system.query.history` 按 `statement_text ILIKE '%<table_or_symbol>%'` 与 紧凑的 `start_time` 窗口，浮现 可能驱动变更的贵查询（按 `total_duration_ms` 排序或聚合 `SUM(read_bytes)`、`COUNT(*)`）。
5. **dbt 血缘。** 若目标读或写 `<your_analytics_db>.<schema>` 模型，模型自身 git 历史（本 repo）常带理由。将该线索交还 git 调查员，不要自己追。

## 这里怎样算好证据

除上述模式外：

- 错误分类 event 计数在防御代码 PR 后几天近零。暗示 PR  解决了该错误类
- 曝光表行 命名了目标的 feature-flag 键，PR 发布日期附近有「shipped」/「concluded」决策

## 常见陷阱

- **有 instrument 不等于因此添加。** event 存在说明有人 care 记录，非目标代码因此存在。与 git 调查员的 PR/commit 引用配对再声称因果。
- **静默 埋点变更。** 事件流量阶跃可能是新事件开始记录，非用户行为变。同窗口查 instrumentation PR 再 将上升解读为功能发布信号。
- **Schema 漂移。** 事件属性会演变。typed dbt 模型上今天的列写目标时可能不存在。旧数据可能只在 raw `properties_json` 里。
- **dbt 刷新滞后。** `<your_analytics_db>.<schema>.*` 按计划重建（常 hourly/daily）。最近几小时 events 回退 `your_warehouse.events.*` 并按 `_id` dedup。
- **公司特定表。** 实验、功能开关、计费、使用表各异。从未确认存在的表报结果是经典失败模式。先 `SHOW TABLES` / `DESCRIBE TABLE`。
- **保留期截止。** 相关窗口早于表 保留期 或 dbt 模型创建日期是*缺口*，非空。明确命名以免合成器读「无结果」为「无活动」。
- **Notebook 不可查。** SQL MCP 看不到 Databricks notebook。若怀疑理由在其中，返回缺口。

## 要返回什么

每项相关发现：
- 类型（product event / experiment exposure / usage or billing event / system-table row / dbt model）
- 全限定表名与 确切查询
- 查询时间窗口
- 紧凑数值摘要（计数、分位数、首次/末次时间戳）。**不要倾倒原始行。**
- 与目标发布日期的时间关联（如「首行 2024-08-15，PR #49074 2024-08-14 合并」）
- 相关性 + 强度：直接 / 旁证 / 弱
