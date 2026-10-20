---
title: "配方与坑"
description: "值得照抄的提示词，加上每个人都会犯一次的错。用的时候，把路径和完结条件换成你自己的。"
sourceUrl: "https://github.com/cursor/plugins/blob/d457567332aa6989e753ea289f30ea2bfbc80bc0/pstack/docs/guide/10-recipes-and-pitfalls.md"
meta:
  updated_at: "2026-10-19T09:35:18+08:00"
  updated_by: "kimi-code-cli"
  triggered_by: "routine pstack-daily-translate"
  translation:
    model: "kimi-code/k3"
    effort: "default"
    translated_at: "2026-10-19T09:35:18+08:00"
    source_version: "0.15.27 / f9be7aa"
---

> [!NOTE]
> **来源**
>
> 这是中文译文，方便对照阅读。本站不发行中文版插件。
>
> 对照英文：[本站英文页](https://pstack.ganhai.cloud/en/skills-zh/official-guide/10-recipes-and-pitfalls/)
>
> 英文原文出处：cursor/plugins 仓库 [`pstack/docs/guide/10-recipes-and-pitfalls.md`](https://github.com/cursor/plugins/blob/d457567332aa6989e753ea289f30ea2bfbc80bc0/pstack/docs/guide/10-recipes-and-pitfalls.md)（提交 `d457567`）

# 配方与坑

先是值得照抄的提示词，然后是每个人都会犯一次的错。用的时候，把路径和完结条件换成你自己的。这些配方故意写得很随意。平时大家就是这么敲的，skill 也读得懂意图。

![她在尝一道做好的菜，机器人照着食谱盒里的菜谱做饭。台面上方钉着几张卡片，分别写着 /how、/tdd 和 /loop。](https://pstack.ganhai.cloud/guide/recipes.jpg)

## 弄清一个陌生的子系统

```text
use /how first to understand how this initialization works. then use /why to figure out why it broke recently.
```

先看机制，再看历史。每个 skill 的报告都会写明它查了哪些来源，你就知道答案依据的是什么。

## 先重述吵闹的报告，再碰代码

```text
/poteto-mode read this thread. restate the underlying issue in your own words, in plain english. don't change any code yet.
```

读错了，会先出现在重述里，纠正只要一条消息。你自己的猜测先留着，等 agent 说出它自己的理解。`/in-your-own-words` 只让它重述，不走 playbook。

## 开工前先对准任务

```text
/sharpen write the onboarding doc for the new billing service.
```

agent 会问，专家会怎么做这项任务，你真正想要什么，以及任务要求是否说得太多或太少。它会保留一项外部检查来确认结果正确，然后开始干活。

## 先做原型再挑选

```text
/poteto-mode prototype a few options for the settings layout. put them behind a switcher and send me screenshots of each.
```

你从跑起来的东西里挑，不从描述里挑。布局和耗时这类问题，agent 会沿路自己回答。

## 把已经定下的设计写成计划

```text
/poteto-mode turn this design into a plan. small verifiable PRs, each with its own proof.
```

等设计定了再要。计划就是交付物，它会点名接下来执行用的 playbook。

## 给设计再要一个意见

```text
ask /arena for a second opinion on this thread and our approach
```

你现在的设计会成为几个候选之一。最后的综合结论会告诉你，评审团是找到了更好的方案，还是确认了你原来的方案。相比一个以后推翻代价很高的设计，这一步成本很低。

## 并行检查互相独立的切片

```text
/swarm check every package under packages/ against its check.sh. one worker per package. one report.
```

每个 worker（并行干活的子代理）负责一个包。父代理等所有切片都有了结果，再交回一份报告，结论是 `PASS`、`ISSUES` 或 `BLOCKED`，不会把 worker 的原始输出直接倒给你。

## 带着怀疑复审一个分支

```text
/interrogate the whole branch, but skeptically. don't change anything yet. no nitpicks unless it's an actual bug or regression in behavior.
```

这些限定语是真起作用的。「don't change anything yet」让它只读不改。不许挑小毛病的那条要求事先滤掉了噪声，所以标成 `Act on` 的 finding（审查发现的问题）都值得你花时间。

## 用一个失败测试修 bug

```text
/poteto-mode repro the duplicate write first. if there's a cheap test path, /tdd it. then fix and rerun.
```

「if there's a cheap test path」这句很要紧。靠脆弱的 mock 硬凑出来的测试，证明的东西还不如直接跑一遍真实命令。playbook 也有权直说这一点。

## 复现并带着证据修好一份报告

```text
/poteto-mode repro this with /verify-<app>. if it repros on main, fix it and show me a video as proof.
```

「if it repros on main」让 bug 已经不在时，这次运行可以早停。视频让你在读 diff 之前，先核对这次修复。

## 证明测试很难覆盖的性质

```text
/create-formal-verification-skill the retry queue. a message must never be delivered twice.
```

它会在规定范围内的所有情况下检查一条性质。如果代码已经违反这条性质，你会拿到用代码概念表达的反例。它还会生成一个项目内的 skill，供下一个 agent 在相关代码改动时运行。

## 发出去之前先核对数

```text
/benchmark-checklist vet this 40% speedup before it goes in the pr description
```

你会得到更快、更慢、测不出差别，或 inconclusive，并带上跑了几轮、范围，以及是什么在限制这个数字。

## 别再手改同一个错

```text
/correct agents keep adding new config flags without registering them in the schema
```

修复会落在仓库里，变成架构、类型、lint 或测试，下一个 agent 就犯不了这个错。

## 先问怎么做，先不动手

```text
/poteto-help how do i get poteto-mode to stay on every turn?
```

你会得到一份回答、一条可以发出去的提示词，以及一条指到来源的链接。那条提示词发出去之前，什么都不会跑。

## 你离开时让运行保持诚实

```text
im going to bed, keep going autonomously until every fixture passes. do not stop. keep a decision log i can audit in the morning.
```

完整的交接约定在[过夜那一页](./07-overnight.md)。如果任务和完结条件已经在对话里讲过，用这个简短版本就够了。

## 把跑偏的运行拉回来

纠偏的提示词，一行就够：

```text
i said the goal is to repro. i did not ask for a fix yet.
```

```text
apply prove it works. show me the real output, not the build log.
```

```text
/unslop that, no emdashes
```

你很少需要多说。你需要的是叫对名字，[原则那一页](./08-principles.md) 就是这套词汇。

## 让回复说人话

```text
/bro
```

整条提示词就这些。[`/bro`](../skills/bro/SKILL.md) 会把上一条消息重说一遍，像一个人跟另一个人说话那样，不用行话，也更短。一条回复技术上很周全，你读完却还是不知道它说了什么，这时就用它。

## 快速了解聊天的新进展

```text
/ketchup
```

[`/ketchup`](../skills/ketchup/SKILL.md) 用大白话告诉你上一条消息之后变了什么，再列出每项等你拍板的事，附上它建议的选择、证据和风险。它只报告，不做任何改动。

## 常见的坑

- **在提示词里把 skill 一个个列出来。** 「use /how then /architect then /arena」会打乱 playbook 已经排好的步骤。说清目标和约束就行。只有想改掉某个默认选择时，才点名 skill。
- **完结条件含糊。** 「make it better」没给 `/loop` 留下任何可检查的东西。给一个能判定通过或失败的命令或产物。
- **一上来就抛出你对原因的猜测。** agent 会往你指到的地方搜。先让它重述问题，再分享你的直觉。
- **接住第一版设计。** 只试一次，模型最先想到的形状就定死了。要几份原型或 `/architect`，再从证据里挑。
- **打磨一份抽象计划。** 对着还没有代码的计划做对抗审查，会编出永远不会发生的风险。先用原型把还没定的问题定下来，再审已经做出来的东西。
- **几个并行的 agent 挤在同一个 worktree（Git 的独立工作目录）里。** 它们会互相覆盖，谁也分不清每处改动出自哪个 agent。把它们当成 cloud agent 来跑，或者说一句「own worktree per attempt」。
- **还信不过循环就开循环。** 不能自己验证工作的循环，只会更快地造出没核对过的活。先把验证 skill 跑起来。
- **相信一个没核对过的数字。** 热缓存或一条没跑到的代码路径，都能假造加速。数字发出去之前，先跑 `/benchmark-checklist`。
- **同一个错靠手改。** 聊天里纠正一次，只帮这一轮。`/correct` 改仓库，后面的运行就不会再犯。
- **拿 `/arena` 做覆盖检查。** `/arena` 把同一份设计或代码简报重复跑几遍，再选一份作基座，把最好的部分嫁接上去。`/swarm` 把工作拆成切片，或按事先声明的几路竞速分开跑，最后汇总成一份报告。
- **审查意见照单全收。** 不管是机器人还是人，交上来的清单里都是真问题和噪声混在一起。`/interrogate` 会把 finding 分成「要处理」和「驳回」两堆，每条都附上理由。哪一条你都可以改判到另一堆。
- **把 `auto` 当成模型 slug。** `auto` 和 `inherit-parent` 的意思是「不填 model 字段，让子代理沿用父对话的模型」。[安装那一页](./01-setup.md) 讲了这些角色。
- **凭构建变绿就报告成功。** 构建只能证明代码能编译。去要真实的命令、流程、存下来的值或性能剖析结果，并且要求回复里附上证据。
- **自己手写 `SKILL.md`。** 让它走 [Authoring or modifying a skill playbook](../skills/poteto-mode/playbooks/authoring-a-skill.md)，这样才会有校验和复审。

指南到这里就结束了。如果你是跳着读的，回到[安装那一页](./01-setup.md)，跑一个真实的任务。习惯是靠用养成的，不是靠读。

回到[指南目录](./index.md)。
