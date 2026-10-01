---
name: why
description: "用于「为什么 X 是这样实现的」「为什么选了 Y」、设计理由、回归、事后复盘，或数据支撑的阈值问题。发现可用 MCP 并并行查询各证据类别（源码控制、工单跟踪、长文文档、实时聊天、基础设施可观测性、错误跟踪、产品分析数仓），然后返回带引用的决策与权衡解读。运行时行为请用 how。"
disable-model-invocation: true
---

# Why

调查代码背后的动机与意图。

`how` skill 的配套技能。`how` 回答代码做什么、如何工作。`why` 回答是什么力量塑造了它的形态。

下面每次 spawn 都对应 `pstack-models.mdc` 规则中的一行角色及其默认值。将 `model` 设为该行值；若规则或该行缺失，则用默认值。当值为 `auto` 或 `inherit-parent` 时，不设置 `model`。若 Task 工具拒绝某 slug，改用默认值并说明。若默认值也被拒绝，从其错误信息中选取同系列最接近的有效 slug。

## 操作姿态

以**谨慎、保守、精确的调查者**身份工作。诚实区分已知与推断。完整置信度框架与措辞指南见 `references/epistemics.md`。合成器必须遵循它。

## 第 1 步：理解目标与问题

解析用户在问什么。**目标**通常是某段代码、某种模式、某个功能，或一项具名设计决策。**问题**通常是设计理由、权衡、驱动边缘情况的动机、外部约束、死代码，或一次广泛的历史梳理。

若目标模糊（例如「我们为什么这样做？」却没有明确指代），根据对话上下文（打开的文件、最近编辑、光标位置、刚讨论的内容）做最佳猜测。简要说明你的理解，以便用户纠正，然后继续。

## 第 2 步：建立代码锚点

在 spawn 调查员之前，把调查锚定在具体代码上。你需要：

- 相关文件路径与行范围
- 关键符号（函数名、类名、常量）
- 初始提交列表：最近触及该目标的若干次提交
- 合并提交中的 PR 编号（主题行中的 `(#1234)` 模式）

内联构建这些信息。

```bash
# 对目标行做 blame，获取最后修改提交
git blame -L <start>,<end> <file>

# 完整文件历史（含补丁），跟踪重命名
git log --follow -p -- <file>

# 最近 N 次触及该文件的提交，可见 PR 编号
git log --oneline -20 -- <file>

# 从提交信息提取 PR 编号
git log -1 --format=%B <commit>
```

对任何实质性提交，用 `gh` 拉取 PR 正文与讨论：

```bash
gh pr view <number> --json title,body,author,createdAt,mergedAt,labels,closingIssuesReferences,comments,reviews
```

将其整理为种子上下文（文件路径、符号、提交、PR 编号、关联工单 ID），传给调查员。

## 第 3 步：Spawn 并行调查员（默认姿态）

**默认采用完整并行调查。**

### 发现

spawn 调查员之前，列出 Cursor 环境中可用的 MCP。若有 available-tools 映射则使用；否则检查 Cursor 暴露的 `mcps/` 目录中已启用的 MCP 服务器。

将每个可用 MCP 映射到一个证据类别：

1. 源码控制历史
2. 工单 / ticket 跟踪
3. 长文文档
4. 实时团队聊天
5. 基础设施可观测性
6. 错误 / 异常跟踪
7. 产品分析数仓

源码控制始终可通过 git 与 `gh` 访问。对其余六类，根据 MCP 名称、服务器说明、工具名与资源描述分类。若某 MCP 可归属多类，选与其主要证据最匹配的一类。模糊情况记入覆盖图。

目标是完整的**覆盖图**，而非最小覆盖。记录空结果，不要跳过搜索。

在单条消息中启动所有匹配的调查员，使其并发运行。不要让一个 agent 覆盖多个 MCP。

子 agent 配置（每个）：
- `subagent_type`：`generalPurpose`
- `model`：`why investigators` 行，默认 `grok-4.7-xhigh-fast`
- `readonly`：`false`（agent 模式）。**不要用 readonly/Ask 模式。** 它会剥离 MCP 访问，使依赖 MCP 的调查员完全失效。调查员仍不应写入任何内容。

每个调查员获得：
1. `references/investigator-prompt.md` 中的基础 prompt
2. 所选 MCP 对应的类别 playbook `references/sources/<source>.md`（由 `references/source-playbook.md` 中的示例改编）
3. 若目标代码看起来具有防御性（空值检查、重试逻辑、超时处理、限流、功能开关、出口守卫、OOM 处理），则附加跨切面的 `references/sources/incident-postmortem.md`
4. 第 2 步的代码锚点（文件路径、符号、提交哈希、PR 编号、工单 ID）
5. 用户原始问题

### 调查员名册：每个可用证据类别一名

每个有匹配 MCP 的类别 spawn 一名调查员。每人恰好负责一个工具或 MCP。

每项列出类别及其能独特揭示的「为什么」。用于知道该期待什么回报、某类为空时如何命名缺口，以及（仅在可证明不相关时）如何证明可跳过。

1. **源码控制调查员**。Git 历史、`gh` 查 PR、代码注释、测试。始终 spawn。唯一保证可用的来源。最擅长揭示*实现/评审阶段捕获的理由*。

2. **工单 / ticket 跟踪调查员**（如 Linear、Jira、GitHub Issues、Plane、Shortcut MCP）。最擅长揭示*产品或业务驱动力*。当「为什么」在工程外部时最强。

3. **长文文档调查员**（如 Notion、Confluence、Google Docs、Coda MCP）。最擅长揭示*长文设计理由*。即「为什么」在成为代码之前被写下来的地方。

4. **实时团队聊天调查员**（如 Slack、Discord、Microsoft Teams、Mattermost MCP）。最擅长揭示*从未进入文档的实时讨论*。当源码控制、工单与文档纸面痕迹稀薄时尤其重要。

5. **基础设施可观测性调查员**（如 Datadog、New Relic、Honeycomb、Grafana、Splunk MCP）。基础设施/运行时视角。最擅长揭示*促使写这段代码的基础设施与运行时现实*。当目标对基础设施信号有反应（超时、重试、限流、熔断）时最强。

6. **错误 / 异常跟踪调查员**（如 Sentry、Rollbar、Bugsnag、Airbrake MCP）。最擅长揭示*促使防御性或纠正性代码的具体异常与错误轨迹*。对 catch 块、空守卫、类型检查、重试等防御最强。

7. **产品分析数仓调查员**（如 Databricks、Snowflake、BigQuery、ClickHouse、dbt、Redshift MCP）。产品/数据视角。最擅长揭示*塑造代码的产品与数据现实*。对开关门控代码、实验驱动发布、数据迁移、「这个数字从哪来」类问题最强。

### 何时跳过某调查员

仅在有**明确书面理由**时跳过，且理由写入最终「已查阅来源」一节。两种有效理由：

- **该类别在本环境无可用 MCP**。标为缺口，而非选择。例：「实时团队聊天已跳过。无匹配 MCP，对话记录不可搜索。」
- **来源可证明不相关**，而非「可能不相关」。门槛很高。例：「错误/异常跟踪已跳过。目标是构建期脚本，无运行时路径。」

若范围评估表明是单提交琐碎目标且 PR 描述已含完整答案，可在确认全部七个可用类别搜索都会冗余后**内联回答**。明确说明。应属罕见。

## 第 4 步：合成

spawn 一名合成器子 agent：

- `subagent_type`：`generalPurpose`
- `model`：`why synthesizer` 行，默认 `claude-opus-5-5-max`
- `readonly`：`false`（agent 模式）。合成器质量检查会抽查验证引用，可能需要 MCP。Readonly/Ask 会剥离 MCP 并破坏这一点。

合成器获得：
1. 调查员发现（含空结果与带理由跳过的类别）
2. 第 2 步代码锚点
3. 用户原始问题
4. `references/epistemics.md` 中的认识论框架
5. `references/synthesizer-prompt.md` 中的合成器 prompt 模板

## 第 5 步：呈现

取合成器输出呈现给用户。可轻量编辑以提升清晰度或补充对话上下文，但**不要改写置信度措辞**。

## 输出格式

输出结构见 `references/synthesizer-prompt.md`：问题、相关代码、已经查到的、可以合理推断的、竞争假设、还不知道的、已查阅来源、置信度摘要。可按需调整，但保持置信度分离，且「已查阅来源」每个调查员一行，含无结果或跳过的，并附理由。

「已查阅来源」块之后，若用户的 `why` 问题是改代码的前奏，将溯源发现转为 Preserve / Change / Avoid / Risk 约束集，供规划变更使用。

## 常见失败模式（避免）

- **近因偏误**。假设最近提交即权威。当前形态往往是更早决策的累积。要追溯。

## 参考文件

- `references/epistemics.md`。置信度层级与措辞指南。合成器必须遵循。
- `references/investigator-prompt.md`。调查员子 agent 基础 prompt 模板。
- `references/source-playbook.md`。指向下方类别 playbook 的索引。
- `references/sources/*.md`。每类一份自包含示例 playbook，外加跨切面 `incident-postmortem.md`。给调查员匹配其类别的单文件，并适配可用 MCP。
- `references/synthesizer-prompt.md`。合成器子 agent prompt 模板，含输出格式。
