---
title: "先理解代码"
description: "改没看懂的代码，不易察觉的回归就是这样上线的。/how 讲代码现在怎么做，/why 挖出它为什么长这样，/teach 把两者合成一份讲解，/recall 帮你找回近期的上下文。"
sourceUrl: "https://github.com/cursor/plugins/blob/23e4138daa01c42d4969f7a5465f82704e64f798/pstack/docs/guide/03-understand.md"
---

> [!NOTE]
> **来源**
>
> 这是中文译文，方便对照阅读。本站不发行中文版插件。
>
> 对照英文：[本站英文页](https://pstack.ganhai.cloud/en/skills-zh/official-guide/03-understand/)
>
> 英文原文出处：cursor/plugins 仓库 [`pstack/docs/guide/03-understand.md`](https://github.com/cursor/plugins/blob/23e4138daa01c42d4969f7a5465f82704e64f798/pstack/docs/guide/03-understand.md)（提交 `23e4138`）

# 改代码前先理解它

改自己没看懂的代码，不易察觉的回归就是这样上线的。pstack 给你四种入手的办法。`/how` 讲清代码现在在做什么。`/why` 挖出它为什么长成这样。`/teach` 把两者合成一份讲解。`/recall` 帮你找回自己最近在某个话题上的上下文。

![侦探拿放大镜研究一张机器蓝图，机器人在一旁取案卷。她身后的证据板上，线索分别连到 /how 和 /why 下面。](https://pstack.ganhai.cloud/guide/understanding.jpg)

## 用 `/how` 追踪行为

```text
/how do we dedupe notifications? is there an n+1 when we look up subscribers?
```

直接问你真正想问的。[`/how`](../skills/how/SKILL.md) 会读代码，按资深工程师带你上手这个子系统的深度来回答。它会讲运行时流程、关键类型，以及容易看漏的地方。子系统很大时，它会先派出两到四个只读的探索者分头去看。问题很窄时，它就直接读、直接讲。

## 用 `/why` 挖历史

```text
/why was the retry limit set to five? does the reason still hold?
```

[`/why`](../skills/why/SKILL.md) 干活像侦探查一桩陈年悬案。它从版本控制查起，再把你的 MCP 能接到的各类证据同时查一遍，比如工单系统、长篇文档、团队聊天、可观测性平台、错误追踪和数据分析。报告里每一条都注明出处，直接证据和推断分开写。记录不多时，它会说「appears to」（看起来是）。什么都没查到，也会写进报告，因为「没人写下原因」本身就是答案。

这两个 skill 很自然就能连着用。如果你怀疑眼前这团乱是历史原因造成的，`do why first then how` 就是一条很好的提示词。

## 用 `/teach` 真正理解它

```text
/teach me how this PR changes retries. convince me it fixes the cause and not the symptom.
```

摘要不够用的时候，就用 [`/teach`](../skills/teach/SKILL.md)。它会跑 `/how` 和 `/why`，改动小的话可能只跑其中一个，再把各个 finding（查出来的结论）串成一份平实的讲解，一张图接一张图地往上搭。「convince me」（说服我）这个说法值得抄过来用。这样讲解就成了一套你能挑刺的论证，而不只是带你逛一圈。

## 用 `/recall` 重建你自己的上下文

```text
/recall catch me up on the export work from last week
```

[`/recall`](../skills/recall/SKILL.md) 会翻你自己最近的聊天，再加上大家共享的记录（工单、以前的修复、还在报的错误），交回一份简报，说明事情进展到哪了、下一步做什么。重新捡起一个已经生疏的话题时，就用它。如果你想接着某一个具体的聊天往下做，该用的是下面的 Session pickup playbook，不是 `/recall`。

## 用 Session pickup 接手先前的工作

如果另一个 agent，或者上周的你，把某个分支做到一半就停下了：

```text
/poteto-mode take over this branch. read the decision log, figure out what's done, and continue from there. don't redo finished work.
```

[Session pickup playbook](../skills/poteto-mode/playbooks/session-pickup.md) 把之前留下的 trail（决策和操作的记录）当作可信的依据。它会还原分支状态和当时的决策，指出从哪里接着做，并对照原始目标核实接手过来的那些结论，而不是一切从头推一遍。

**坑：** 别因为「agent 反正会读代码」就跳过这一页的 skills。agent 还没把代码怎么运行理清楚就动手改，往往会在第一个看似合理的地方修掉症状。先跑一遍 `/how`，代价比第二个 bug 小。

下一篇：[设计这次改动](./04-design.md)。
