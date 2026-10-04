---
title: "把它变成你的"
description: "生成你自己的 mode，留下一次会话的教训，写一个只做一件事的 skill，并在信任 skill 改动之前先做盲测。"
sourceUrl: "https://github.com/cursor/plugins/blob/e43c7ee26e0038c6c1fa8380dd34ce86ff94cb2a/pstack/docs/guide/09-make-it-yours.md"
meta:
  updated_at: "2026-10-04T10:24:51+08:00"
  updated_by: "cursor-cloud-agent cursor"
  triggered_by: "pstack-daily-translate routine"
  translation:
    model: "claude-opus-5-5"
    effort: "未记录"
    translated_at: "2026-10-03T20:34:59+08:00"
    source_version: "0.15.6 / 23e4138"
---

> [!NOTE]
> **来源**
>
> 这是中文译文，方便对照阅读。本站不发行中文版插件。
>
> 对照英文：[本站英文页](https://pstack.ganhai.cloud/en/skills-zh/official-guide/09-make-it-yours/)
>
> 英文原文出处：cursor/plugins 仓库 [`pstack/docs/guide/09-make-it-yours.md`](https://github.com/cursor/plugins/blob/e43c7ee26e0038c6c1fa8380dd34ce86ff94cb2a/pstack/docs/guide/09-make-it-yours.md)（提交 `e43c7ee`）

# 把它变成你的

poteto-mode 是一个人的风格。它底下那套机制，也就是 playbook、路由和模型角色，换上你的风格一样好用。这一页讲怎么生成你自己的 mode，怎么留下一次会话的教训，怎么写一个只做一件事的 skill，以及怎么在信任一次 skill 改动之前先测试它。

## 用 `/automate-me` 生成你自己的 mode

```text
/automate-me
```

你不用描述自己的风格，[`/automate-me`](../skills/automate-me/SKILL.md) 会从你的历史里把它读出来。它从你在当前工作区最近的对话记录里，找出反复出现的偏好，看你喜欢怎样的回复、委派、验证、代码、文字和流程。然后它问你，哪些模式真正代表你。它用 Cursor 内置的 `create-skill` 流程起草 `.cursor/skills/<your-name>-mode/SKILL.md`，让 [`/unslop`](../skills/unslop/SKILL.md) 把草稿过一遍，再在一个 worktree（Git 的独立工作目录）里开 PR，好让你像审别的改动一样审它。

习惯变了，就再跑一次：

```text
/automate-me update my mode skill with everything since its last edit
```

更新时，它只看这个 skill 上次改动之后的历史。你没推翻过的 rule 原样保留，有了新证据的 rule 会改写，只有真正新出现的模式才会加一节。

## 用 `/reflect` 留下一次会话的教训

某个任务让你学到了东西，就在它刚结束时运行：

```text
/reflect that took way too long. capture what we learned so the next run doesn't repeat it.
```

[`/reflect`](../skills/reflect/SKILL.md) 把对话记录同时交给三个审查者。然后一个汇总者把它们的提议分进 `Accepted`、`Rejected` 和 `Backlog` 三类，在改动任何 skill 之前先等你批准。只批准那些会改变将来某个决定的提议。一次古怪的会话只是个例，算不上 rule。

## 写一个只做一件事的 skill

如果你已经知道要把哪套工作流写成 skill：

```text
/poteto-mode write a skill for verifying database migrations in this repo
```

写 skill 会匹配到 [Authoring or modifying a skill playbook](../skills/poteto-mode/playbooks/authoring-a-skill.md)。它用 Cursor 内置的 `create-skill` 来写，检查 frontmatter 和链接，最后走 Opening a PR playbook 把结果交出去。写给 agent 看的文字，要求比写给人看的更高。因为一句没用的话，会变成将来某个 agent 照着执行的指令。这个标准交给 playbook 去守，不要自己手写 `SKILL.md`。

有一种特殊情况自带生成器。一个 skill 如果必须操作你的应用、证明它的行为，那它就是验证 skill，这时改用 [`/create-verification-skill`](../skills/create-verification-skill/SKILL.md) 和 [`/maintain-verification-skill`](../skills/maintain-verification-skill/SKILL.md)。[验证结果并开 PR](./06-verify-and-ship.md#create-a-project-verification-skill) 把这两个都讲了。

## 用 `/technical-writing` 按标准写文档

你交出去的文字不只有 skill。写文档、RFC、README、PR 描述和提交说明时，用这个：

```text
/technical-writing review the readme changes
```

[`/technical-writing`](../skills/technical-writing/SKILL.md) 用的是一套分层的标准，目标只有一个，写出疲惫的工程师读第一遍就懂的文字。它先定下文档属于哪一类（tutorial、how-to、reference 或 explanation），再逐句处理：谁做什么，一句只说一个意思，不留能读出两种意思的句子。你或 agent 刚写完的东西，可以用它来审。请 agent 写文档时，也可以一开始就点名用它。

## 盲测 skill 改动

改一次 skill，以后的每次会话都会受影响。这本来就是一次实验，就按实验来测：

```text
/poteto-mode run the eval playbook on this skill change. same task for both variants, candidates stay blind.
```

[Eval playbook](../skills/poteto-mode/playbooks/eval.md) 围绕一种失败模式来设计，这种模式叫观察者效应。agent 一旦知道自己在接受评估，表现就会变。所以候选 agent 拿到的任务，看起来像真实用户的请求，工作目录也去掉了评测的痕迹。它们不会看到「eval」或「candidate」这样的词，也不知道还有别的候选存在。由一个评判者给所有输出打分，它看到的只是中性的标签。候选有没有顺着 skill 链一路读下去，按它实际读过哪些文件来评分，不看它自己怎么说。

接受 verdict（评判结论）之前，每份输出都要自己读一遍。如果你和评判者意见不一致，先怀疑评分标准，再怀疑自己的判断。

**坑：** 某个 skill 表现不对时，不要在任务做到一半去改它。单独开一个 PR 修它，手上的任务继续推进。skill 的改动要是跟功能开发缠在一起交付，复审时看不见，也没法评估。

下一篇：[配方与坑](./10-recipes-and-pitfalls.md)。
