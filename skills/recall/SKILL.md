---
name: recall
description: "从自己的聊天历史、现场状态与共享记录（用户报告、既往修复、事故）重建近期工作上下文，交回一份紧凑的当前状态简报。用于「recall my work on X」「catch me up」「what have I been working on」「where did I leave off」，或在开始/恢复工作前。"
disable-model-invocation: true
---

# Recall

**在开始或恢复工作前，重建用户的近期工作上下文，交回一份紧凑胶囊：当前站哪里、下一步做什么。**

保持紧凑、切题。只读范围内线程所需内容，然后停。

你的上下文分两份记录。自己的聊天历史记录你做了什么、决定了什么。共享记录记录同一批代码下以其他名义发生的一切：用户反复报告的症状、已 ship 又被 revert 的修复、生产仍在报的错误。**why** skill 搜索的就是这份第二记录，跨源码控制、issue tracker、聊天与 issue 频道、长文文档与错误追踪。长尾 bug 多的功能，故事多半在这里，不要只靠 transcript 重建。

Transcript 位于 `~/.cursor/projects/<slug>/agent-transcripts/<uuid>/<uuid>.jsonl`，`<slug>` 为 workspace 路径去掉 leading slash、每个 `/` 换为 `-`（如 `/Users/you/proj` → `Users-you-proj`）。每行一条聊天消息。

1. 分类再路由。恢复某一次具体历史对话是 `session-pickup` playbook，不是本 skill。把习惯固化为长期 skill 是 `automate-me`。人类可读的工作摘要又是另一任务。Recall 是在行动前跨近期对话加载工作上下文。若用户已给完整状态胶囊（路径、分支、改动），直接用，跳过挖掘。
2. 搜索前锁定范围。钉住时间窗口（「最近」是真实区间，默认最近 7 天）、若点名的主题、workspace（默认当前。未被要求时不要读其他项目的 transcript）。把范围复述回去。不要悄悄把「全部」变成「最近 N 条」。
3. 在自己的聊天历史上扇出。在快速、廉价模型上并行 spawn 子 agent，各取语料一片。告诉每个子 agent 按真实修改时间排序候选（`ls -t`），绝不按 UUID 名；先 grep 主题再只读匹配对话的相关区域；跳过当前对话与明显噪声（subagent、eval、test 对话）。每个返回同一 schema，每对话一块：主题、用户目标、决策、开放线程、卡点与纠正、artifact（PR、ticket、分支），并引用对话 UUID。仅一两份对话时跳过扇出，直接搜。原始 transcript 留在子 agent，主线程只收发现。
4. 只要主题点名功能、文件、子系统、区域或 bug，就扫共享记录。这是默认，不是酌情。**why** skill 的 source investigator 负责，但把问题从「为何这样建」导向「当前状态、试过什么没撑住、用户还在报什么」。复用其 per-source playbook，与对话挖掘并行跑 investigator，继承其姿态：每 source 一个 investigator，空结果也是发现，MCP 不可用则跳过并说明。并入简报。仅纯活动回忆且无命名目标（「我这周做了什么」）时跳过，此时自己的历史与现场状态即全部答案。
5. 对照现场状态核实。对挖掘与共享记录 sweep 发现的 PR、分支、ticket 用 `git` 与 `gh` 检查。当答案取决于 agent 实际做了什么（跑了什么工具、读了什么文件、遇到什么错误）时，读完整 transcript，不要只看本地截断副本。
6. 按下方契约写简报。按线程分组。紧扣命名主题。

## 输出要写成什么样

先胶囊，再线程状态，再问题，再下一步。更深细节放下面或删掉。

- **胶囊。** 最多 5 条。这项工作是什么、整体站哪里。
- **线程。** 各一行，前缀恰好一个状态标签：`[merged #N]`、`[open PR #N]`、`[in flight <branch>]`、`[verified, uncommitted]`、`[reverted #N]`、`[planned, not started]`。无标签的线程不算完成，须打标签。
- **问题。** 最多 5 条，反复出现的。含用户持续报告的症状与 ship 后 revert 的修复，使下次尝试从上次失败处开始。
- **下一步。** 一条最有用的具体行动。

相邻功能或 ticket 除非阻塞本项否则不写。胶囊与线程行超一屏时先删细节，不删线程。经 **unslop** skill 写简报；对话发现引用 UUID，共享记录引用其 source（PR #、ticket ID、聊天 permalink、错误追踪 issue）；公开输出前 脱敏私密上下文。

**回复：** 上述契约的简报。
