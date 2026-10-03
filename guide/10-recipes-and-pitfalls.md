---
title: "配方与坑"
description: "可照抄的提示词，以及人人都会犯一次的错。换成你自己的路径和完结条件。"
sourceUrl: "https://github.com/cursor/plugins/blob/23e4138daa01c42d4969f7a5465f82704e64f798/pstack/docs/guide/10-recipes-and-pitfalls.md"
---

> [!NOTE]
> **来源**
>
> 这是中文译文，方便对照阅读。本站不发行中文版插件。
>
> 对照英文：[本站英文页](https://pstack.ganhai.cloud/en/skills-zh/official-guide/10-recipes-and-pitfalls/)
>
> 英文原文出处：cursor/plugins 仓库 [`pstack/docs/guide/10-recipes-and-pitfalls.md`](https://github.com/cursor/plugins/blob/23e4138daa01c42d4969f7a5465f82704e64f798/pstack/docs/guide/10-recipes-and-pitfalls.md)（提交 `23e4138`）

# 配方与坑

值得照抄的提示词，然后是人人都会犯一次的错。换成你自己的路径和完结条件。这些配方故意写得很随意。实际打字时就是这样。skill 读意图没有问题。

![她品尝做好的菜，机器人照着食谱盒做菜。柜台上方钉着写有 /how、/tdd 和 /loop 的卡片。](https://pstack.ganhai.cloud/guide/recipes.jpg)

## 弄清一个陌生的子系统

```text
use /how first to understand how this initialization works. then use /why to figure out why it broke recently.
```

先机制，后历史。每个 skill 的报告会告诉你它搜了哪些来源。你就知道答案依据是什么。

## 给设计再要一个意见

```text
ask /arena for a second opinion on this thread and our approach
```

你当前的设计变成若干候选之一。综合结果会告诉你，评审团是找到了更好的，还是确认了你已有的。在昂贵的承诺之前，这是便宜的保险。

## 并行检查互相独立的切片

```text
/swarm check every package under packages/ against its check.sh. one worker per package. one report.
```

每个 worker 负责一个包。父级等齐每一个切片，然后返回一份 `PASS`、`ISSUES` 或 `BLOCKED` 报告。不返回 worker 的原始输出。

## 带着怀疑复审一个分支

```text
/interrogate the whole branch, but skeptically. don't change anything yet. no nitpicks unless it's an actual bug or regression in behavior.
```

这些限定语在干实事。「don't change anything yet」让它保持只读。挑剔用的 rule 预先滤掉噪声。这样标成 `Act on` 的 finding 才值得你花时间。

## 用一个失败测试修 bug

```text
/poteto-mode repro the duplicate write first. if there's a cheap test path, /tdd it. then fix and rerun.
```

「if there's a cheap test path」这句要紧。逼测试走脆弱的 mock，证明力不如跑真实命令。playbook 可以这么说。

## 你离开时让运行保持诚实

```text
im going to bed, keep going autonomously until every fixture passes. do not stop. keep a decision log i can audit in the morning.
```

完整契约在[过夜那一页](./07-overnight.md)。任务和完结条件已经在对话里时，短形式就够用。

## 把漂走的运行拉回来

转向用的提示词只有一行：

```text
i said the goal is to repro. i did not ask for a fix yet.
```

```text
apply prove it works. show me the real output, not the build log.
```

```text
/unslop that, no emdashes
```

你很少需要更多的词。你需要的是对的名字。[原则那一页](./08-principles.md) 就是这套词汇。

## 让回复说人话

```text
/bro
```

这就是整条提示词。[`/bro`](../skills/bro/SKILL.md) 把上一条消息重说一遍，像一个人跟另一个人说话。没有行话，更短。回复在技术上很全，你却还是不知道它说了什么。这时用它。

## 这些坑

- **在提示词里枚举 skill。** 「use /how then /architect then /arena」会重排 playbook 已经排好的步骤。说出目标和约束。只有要覆盖默认时才点名 skill。
- **模糊的完结条件。** 「make it better」没给 `/loop` 任何可检查的东西。给一个能通过或失败的命令或产物。
- **多个代理挤在一个 worktree。** 它们互相覆盖，diff 变成考古。说「own worktree per attempt」，隔离不用另花代价。
- **用 `/arena` 做覆盖。** `/arena` 把同一份设计或代码简报重复多份，然后选出一个基座，把最好的部分接上去。`/swarm` 划分切片或声明好的 race arm，并汇总成一份报告。
- **照单全收每条审查评论。** 机器人和人把真实捕获和噪声放在同一张列表里。`/interrogate` 把 finding 分成 act-on 和 dismissed 两桶，并附理由。你可以从两边改判。
- **把 `auto` 当成模型 slug。** `auto` 和 `inherit-parent` 的意思是「省略 model 字段，让子代理继承父对话的模型」。[配置](./01-setup.md) 讲这些角色。
- **凭一次绿色构建报告成功。** 构建证明它能编译。去要真实命令、流程、存下的值或 profile，并期待回复里有证据。
- **手写一份 `SKILL.md`。** 把它交给 [Authoring or modifying a skill playbook](../skills/poteto-mode/playbooks/authoring-a-skill.md)，校验和复审才会发生。

指南到这里。如果你跳着读了，回到[配置](./01-setup.md)，跑一个真实任务。习惯来自使用，不来自阅读。

回到[指南目录](./index.md)。
