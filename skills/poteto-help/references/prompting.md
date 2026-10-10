---
description: 提示词写清意图和完结条件。步骤由 playbook 提供，几句白话就够。
---

:::note[导读]
写 `/poteto-mode` 提示词时看这一页。写清目标和完结条件，步骤交给 playbook。
:::

# 写好提示词

提示词写清意图，以及怎样算做完。步骤由 playbook（针对某类任务写好的一套步骤）提供，所以几句白话比一份规格更管用。

## 写进去

- 目标。说清哪里不对，或者对方想要什么。
- 完结条件。它能判定通过或失败。「Make it better」和一段时间都不是检查。
- 要看的证据。去要真实的命令输出、流程的录像、存下来的值，或改前改后的数字。
- 对方已经知道的。一个症状、一条复现步骤、一份日志或一个链接，都能省掉 agent 去搜。
- 真正的约束。「repro first」「don't change any code yet」「zero behavior change」和「let me review before proceeding」每条都会改变 agent 做什么。

## 不要写

- 怎么做。说清要达成什么，留给 agent 空间去找更好的办法。
- 一串 skill 或步骤。手写的顺序会漏掉或打乱 playbook 会保留的步骤。只有想推翻某个选择时，才点名 skill。
- 对方对根因的猜测，直到 agent 先用自己的话重述问题。先抛出猜测，会收窄搜索。

## 先装上上下文

- 报告很吵时，先让 agent 用自己的话、用明白的英文，重述底下真正的问题，再做别的。读偏了，会在还没有代码之前就露出来。
- 新开的聊天里，用 `/recall` 找回这个话题上先前的工作。旧聊天里有新 agent 没有的上下文。
- 改不熟的代码之前，用 `/how` 问机制，用 `/why` 问原因。agent 若还没有追过代码怎么运作，就会在第一个说得通的地方修症状。
- 让 `/teach` 为某个选择做论证，比如「convince me it fixes the cause and not the symptom」。论证比摘要更好核对。

## 先设计，再写计划

- 不要收下第一个设计。让它先做几个方案的原型，界面就配截图或录像，再凭证据来选。
- 开放问题交给原型来回答。不要拿一份抽象计划去做对抗式审查，审查者会编造根本不会发生的风险。
- 共享的包或 API，先要 README 或一份 tutorial，再回到代码。这份文档会变成 agent 核对自己的目标。
- 设计定了再要计划。计划的每一步都以一次检查收尾。

## 后续可以很短

- 对话里已经有了任务时，「do it」「continue」和「keep going until done」就是整条提示词。
- 换话题时，用「new task」开头。不然 mode 会把这条消息当成下一步。

## 走开之前

- 说「im going to bed」或「im stepping away」，agent 就不再追问。
- 把「做完」写成每一轮都能跑的检查，并把这个判定条件交给 `/loop`。
- 要求从一个点名的基分支切出一份新的 worktree（Git 的独立工作目录）。
- 预先答好 agent 会停下来问的事，比如「don't ask me before committing」。
- 要求留一份决策日志，方便以后审计。
- 给一个退出：「if you're truly stuck after a few hours, stop and write up why」。

## 一行纠偏

- 重申目标：「i said the goal is to repro. i did not ask for a fix yet.」
- 点名原则：「apply prove it works. show me the real output, not the build log.」
- 原则名管用，是因为 agent 已经读过这条规则。它的回复会点出这条规则改变了哪个决定。
