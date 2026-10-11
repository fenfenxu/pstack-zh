# Datadog 遥测

## 这个来源里有什么

Datadog 记录运行时——生产实际发生什么，而非计划或讨论。

- **指标。** 团队埋点的计数器、仪表、直方图。一条指标*存在*，本身就说明有人觉得这个数字值得看。
- **监控与告警。** 团队认为值得叫醒人的条件。对 `rate_limit_hit > 10/min` 告警的 monitor 是直接证据：团队担心该阈值。
- **仪表盘。** 整理过的视图，看出团队认为这个子系统里什么重要。
- **APM 追踪与 span。** 请求级的运行时数据，用来回答「为什么慢」或「为什么这里会超时」。
- **日志。** 量大的事件记录，里面常常就是促使人写下防御代码的那些错误。
- **事故。** 正式事故记录，含时间线与链接事后复盘。
- **Notebook。** 团队自己做的探索性调查，里面常常有假设和分析。

## 怎么搜

使用 Datadog MCP。先广后窄。

1. **识别负责服务。**

   ```
   search_datadog_services (filter by name or team)
   search_datadog_service_dependencies (see upstream/downstream)
   ```

2. **先看仪表盘和 monitor。**

   ```
   search_datadog_dashboards (query: feature name, service name, symbol)
   search_datadog_monitors   (same queries)
   ```

   当仪表盘或 monitor 覆盖目标时，记下其查询与监控阈值。阈值常是「为何限制为 N」的答案。

3. **目标相关指标。**

   ```
   search_datadog_metrics (by name pattern, e.g., the feature or symbol)
   get_datadog_metric_context (metadata: description, units, tags)
   get_datadog_metric (timeseries; "was there a spike around the PR date?")
   ```

   将指标轨迹与目标变更日期关联是强旁证：「`payment_timeout` 指标在 2023-11-03 尖峰，重试逻辑 2023-11-06 合并。」

4. **日志。收窄，不要倾倒。**

   ```
   search_datadog_logs (raw log patterns near the target, set use_log_patterns=true)
   analyze_datadog_logs (SQL-style aggregations, only when you need counts)
   ```

   用符号、错误字符串或功能名搜索。**强烈建议限定时间**（例如变更前后 30 天）。

5. **APM span 与 trace。**

   ```
   aggregate_spans    (stats: "how often does this endpoint fail?")
   search_datadog_spans (inspect individual spans)
   get_datadog_trace  (a specific trace ID)
   ```

   适用于超时、重试、慢路径、跨服务行为。

6. **事故。**

   ```
   search_datadog_incidents (by title, team, date range)
   get_datadog_incident     (full detail for a specific incident)
   ```

   若目标看起来防御性，搜添加前后的事故。时间线含「为 X 加防御检查」的事故接近直接证据。

## 这里怎样算好证据

- monitor 查询与阈值匹配代码所强制约束（代码限制到 100，monitor 在请求超 100/min 告警）
- 目标作者创建的仪表盘，组件对应代码测量或防护的内容
- 合并前生产尖峰、合并后稳定的指标
- 引用目标代码、同符号或同错误串的事故记录
- 防御代码会阻止的错误模式日志，时间戳在变更前窗口

## 常见陷阱

- **相关不等于因果。** PR 前有尖峰、之后稳住，这只是提示，不是定论，因为同一窗口里可能还有别的改动。去查相邻的 PR。
- **过度贴合找到的图。** 一张图反映的是做图人自己的角度。名叫「retry success rate」的图，说明团队在意重试是否成功，不能说明某一行代码就是因此而存在。
- **遥测消失。** 指标可改名、删除或保留期短。相关窗口无数据是缺口，非空结果。
- **规模噪声。** 搜日志时按 service、tag 和时间窗口尽量收窄。用 `analyze_datadog_logs` 做聚合，不要把原始日志倒出来。
- **有埋点不等于因此添加。** 有一条指标，只说明有人在意到愿意去量，不能说明代码是*因为*它才加上的。和 commit、PR 的日期对一下。

## 要返回什么

每项相关条目：
- 类型（仪表盘 / 监控 / 指标 / 日志模式 / 追踪 / 事故 / 笔记本）
- 标题或名称
- 链接或标识（dashboard ID、monitor ID、指标名、incident ID）
- 所有者/作者与创建/修改日期
- 与问题相关的具体条件、查询或引用（尽可能逐字）
- 相关性：对目标代码暗示什么，连接强度如何
