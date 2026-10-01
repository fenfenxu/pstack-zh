---
name: how
description: "用于「X 如何工作」、改代码前的代码走读，以及归属/分层问题（「该放哪」「哪个包负责」「是不是对的层」）。解释子系统架构、运行时流程、上手心智模型。动机与理由请用 why。"
disable-model-invocation: true
---

# How

探索代码库，回答「X 如何工作」类问题。产出资深工程师接手子系统级别的架构说明——足以建立可用的心智模型，又不至于读起来像带注释的源码。

下面每次 spawn 都对应 `pstack-models.mdc` 规则中的一行角色及其默认值。将 `model` 设为该行值；若规则或该行缺失，则用默认值。当值为 `auto` 或 `inherit-parent` 时，不设置 `model`。若 Task 工具拒绝某 slug，改用默认值并说明。若默认值也被拒绝，从其错误信息中选取同系列最接近的有效 slug。

## 第 1 步：评估复杂度

若范围模糊，说明你的理解并探索。用户可纠正方向。

- **简单**（单模块、小工具、窄问题如「函数 X 如何工作」）：不 spawn 探索者。一名讲解者一次完成探索与说明。进入第 2b 步。
- **复杂**（跨多文件/服务的子系统、横切功能、完整架构概览）：先 spawn 并行探索者，再交给讲解者。进入第 2a 步。

有疑则走简单路径。

## 第 2a 步：探索（仅复杂问题）

将问题拆成 2 到 4 个探索角度，每个是子系统的不同切片。在单条消息中 spawn 所有探索者：

- `subagent_type`：`generalPurpose`
- `model`：`how explorer` 行，默认 `grok-4.7-xhigh-fast`
- `readonly`：`true`

每个探索者获得 `references/explorer-prompt.md` 中填好角度的 prompt。然后进入第 3 步。

## 第 2b 步：直接讲解（简单问题）

spawn 一名 Task 子 agent，一次完成探索与说明：

- `subagent_type`：`generalPurpose`
- `model`：`how explainer` 行，默认 `claude-opus-5-5-max`
- `readonly`：`true`

从 `references/explainer-prompt.md` 构建 prompt，不含探索者发现节。进入第 4 步。

## 第 3 步：综合（仅复杂问题）

所有探索者返回后，spawn 一名 Task 子 agent，将发现综合为一份说明：

- `subagent_type`：`generalPurpose`
- `model`：`how explainer` 行，默认 `claude-opus-5-5-max`
- `readonly`：`true`

从 `references/explainer-prompt.md` 构建 prompt，填入各探索者的发现。

## 第 4 步：呈现

将讲解者输出呈现给用户。可轻量编辑以提升清晰度或补充对话上下文。不要大幅改写。

## 输出格式

说明采用 `references/explainer-prompt.md` 定义的章节，不适用的可省略：概览、关键概念、工作原理、文件分布、注意事项。
