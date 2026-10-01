---
title: "先理解代码"
description: "改代码之前先弄清它。/how 讲现状，/why 挖原因，/teach 合成说明，/recall 重建你自己的近期上下文。"
sourceUrl: "https://github.com/cursor/plugins/blob/12d587dfb20741cafc376c42c696c5f6e2a64487/pstack/docs/guide/03-understand.md"
---

> [!NOTE]
> **来源**
>
> 这是中文译文，方便对照阅读。本站不发行中文版插件。英文原文 [`pstack/docs/guide/03-understand.md`](https://github.com/cursor/plugins/blob/12d587dfb20741cafc376c42c696c5f6e2a64487/pstack/docs/guide/03-understand.md)，取自 pstack 官方仓库 cursor/plugins 的提交 `12d587d`。
>
> [本站英文页](https://pstack.droplink.cloud/en/skills-zh/official-guide/03-understand/)

# 改它之前先理解代码

改你不理解的代码，细微的回归就是这样发出去的。pstack 给你四条进入的路。`/how` 解释代码现在做什么。`/why` 挖出它长成这样的原因。`/teach` 把两者揉成一份说明。`/recall` 重建你自己近期关于某个话题的上下文。

![侦探用放大镜研究一张机器蓝图，机器人在取案卷。她身后的证据板把线索连在 /how 和 /why 下面。](https://pstack.droplink.cloud/guide/understanding.jpg)

## 用 `/how` 追踪行为

```text
/how do we dedupe notifications? is there an n+1 when we look up subscribers?
```

问你真正想问的。[`/how`](../skills/how/SKILL.md) 读代码，按资深工程师把你带进这个子系统的层级来回答，带上运行时流程、关键类型，以及不明显的部分。大子系统会先扇出两到四个只读探索者。问题窄，它就直接读并解释。

## 用 `/why` 挖历史

```text
/why was the retry limit set to five? does the reason still hold?
```

[`/why`](../skills/why/SKILL.md) 的工作方式像查一桩冷案。它从源代码管理出发，再去查你的 MCP 能提供的证据，例如议题追踪、长文文档、团队聊天、可观测性、错误追踪和分析。这些查询一起跑。报告会引用每一项，并把直接证据和推断分开。记录很少时，它会说「appears to」。没有结果也要写进报告。「没人写下为什么」本身就是答案。

两者自然组合。当你怀疑是历史在解释这团乱时，`do why first then how` 就是一条完全合适的提示词。

## 用 `/teach` 真正理解它

```text
/teach me how this PR changes retries. convince me it fixes the cause and not the symptom.
```

[`/teach`](../skills/teach/SKILL.md) 用在摘要不够的时候。它会跑 `/how` 和 `/why`。改动很小的话，也许只跑其中一个。然后把 finding 收成一份白话说明，一张图一张图搭起来。「convince me」这种问法值得留着。说明会变成你可以反驳的论证，而不是一遍导览。

## 用 `/recall` 重建你自己的上下文

```text
/recall catch me up on the export work from last week
```

[`/recall`](../skills/recall/SKILL.md) 挖掘你自己近期的聊天，加上共享记录（议题、先前的修复、仍在触发的错误），交回一份简报：事情停在哪里，下一步是什么。你冷着回到一个话题时用它。如果你要恢复某一个具体聊天，那是下面的 Session pickup playbook，不是 `/recall`。

## 用 Session pickup 接手先前的工作

另一个 agent（或你，上周）把一个分支留在半途时：

```text
/poteto-mode take over this branch. read the decision log, figure out what's done, and continue from there. don't redo finished work.
```

[Session pickup playbook](../skills/poteto-mode/playbooks/session-pickup.md) 把先前的 trail 当作权威。它重建分支状态和决策，指出恢复点，并对照原始目标验证继承下来的说法，而不是从零重新推导一切。

**坑：** 不要因为「agent 反正会读代码」就跳过这一页的 skills。agent 还没把模型追出来就开始改，往往会在第一个说得通的地方修症状。先跑 `/how`，比再出一个 bug 便宜。

下一篇：[设计这次改动](./04-design.md)。
