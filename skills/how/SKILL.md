---
name: how
description: "用于「how does X work」、改东西之前的代码走读，以及代码该放哪、归谁管、属于哪一层这类问题（「where should this live」「which package owns this」「is this the right layer」）。讲解子系统架构、运行时流程，以及上手所需的心智模型。想知道动机，用 why。"
disable-model-invocation: true
---

# How

探索代码库，回答「X 是怎么工作的」这类问题。讲解的深度，以资深工程师刚接手一个子系统时的需要为准。要足够建立一个能用的心智模型，又不能细到读起来像加了注释的源码。

下面每次启动子代理，都写明它对应 `pstack-models.mdc` 这条 rule 里的哪一行角色配置，以及默认值。把 `model` 设为那一行的值。rule 或那一行不存在时，设为默认值。值为 `auto` 或 `inherit-parent` 时，不设 `model`。Task 工具拒绝某个 slug 时，改用默认值，并说明这一点。Task 工具连默认值也拒绝时，从它的报错信息里挑同一系列中最接近的有效 slug。

## 第 1 步：评估复杂度

范围不明确时，说出你的理解，然后开始探索。用户可以再调整方向。

- **简单**（单个模块、一个小工具，或「函数 X 是怎么工作的」这样的窄问题）：不启动探索者。由一个讲解者一次完成探索和讲解。转到第 2b 步。
- **复杂**（跨多个文件或服务的子系统、横跨多处的功能、完整的架构总览）：先并行启动探索者，再交给讲解者。转到第 2a 步。

拿不准时，走简单路径。

## 第 2a 步：探索（仅复杂问题）

把问题拆成 2 到 4 个探索角度，每个角度对应子系统里不同的一块。在同一条消息里启动全部探索者：

- `subagent_type`：`generalPurpose`
- `model`：`how explorer` 那一行，默认 `grok-4.7-xhigh-fast`
- `readonly`：`true`

每个探索者拿到的提示词，是 `references/explorer-prompt.md` 填上各自角度后的版本。然后转到第 3 步。

## 第 2b 步：直接讲解（简单问题）

启动一个 Task 子代理，一次完成探索和讲解：

- `subagent_type`：`generalPurpose`
- `model`：`how explainer` 那一行，默认 `claude-opus-5-5-max`
- `readonly`：`true`

用 `references/explainer-prompt.md` 拼出它的提示词，去掉探索者的 finding（探索中查到的事实和结论）那一节。转到第 4 步。

## 第 3 步：综合（仅复杂问题）

所有探索者都返回后，启动一个 Task 子代理，把它们的 finding 综合成一份讲解：

- `subagent_type`：`generalPurpose`
- `model`：`how explainer` 那一行，默认 `claude-opus-5-5-max`
- `readonly`：`true`

用 `references/explainer-prompt.md` 拼出它的提示词，填入每个探索者的 finding。

## 第 4 步：呈现给用户

把讲解者的输出呈现给用户。为了更清楚，或为了补上对话里的上下文，可以小改。不要大幅改写。

## 输出格式

讲解使用 `references/explainer-prompt.md` 里定义的几节，不适用的就去掉：概览、关键概念、工作原理、代码位置、容易踩的坑。
