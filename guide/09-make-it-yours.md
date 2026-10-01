---
title: "把它变成你的"
description: "生成个人 mode，从会话留下教训，编写聚焦的 skill，并在信任之前盲测改动。"
sourceUrl: "https://github.com/cursor/plugins/blob/12d587dfb20741cafc376c42c696c5f6e2a64487/pstack/docs/guide/09-make-it-yours.md"
---

> [!NOTE]
> **来源**
>
> 这是中文译文，方便对照阅读。本站不发行中文版插件。英文原文 [`pstack/docs/guide/09-make-it-yours.md`](https://github.com/cursor/plugins/blob/12d587dfb20741cafc376c42c696c5f6e2a64487/pstack/docs/guide/09-make-it-yours.md)，取自 pstack 官方仓库 cursor/plugins 的提交 `12d587d`。
>
> [本站英文页](https://pstack.ganhai.cloud/en/skills-zh/official-guide/09-make-it-yours/)

# 把它变成你的

poteto-mode 是一个人的风格。底下的机器，playbook、路由、模型角色，换成你的风格照样工作。本页讲怎么生成你自己的 mode，怎么从一次会话留下教训，怎么写一个只做一件事的 skill，以及怎么在信任之前先测 skill 的改动。

## 用 `/automate-me` 生成你自己的 mode

```text
/automate-me
```

你不用描述自己的风格。[`/automate-me`](../skills/automate-me/SKILL.md) 会从你的历史里读出来。它在当前工作区挖掘你最近的对话记录，找重复的偏好：你喜欢怎样的回复、委派、验证、代码、正文和流程。然后问你哪些模式真的是你。它通过 Cursor 内置的 `create-skill` 流程起草 `.cursor/skills/<your-name>-mode/SKILL.md`，再把草稿交给 [`/unslop`](../skills/unslop/SKILL.md)，并从 worktree 开一个 PR，让你像审其他改动一样审它。

习惯漂了就再跑一次：

```text
/automate-me update my mode skill with everything since its last edit
```

更新 mode 只挖掘这个 skill 上次改变之后的历史。你没否定过的 rule 会保留。有了新证据的会改。只有真正的新模式才会加节。

## 用 `/reflect` 留下一次会话的教训

一件任务刚教会你东西，就运行：

```text
/reflect that took way too long. capture what we learned so the next run doesn't repeat it.
```

[`/reflect`](../skills/reflect/SKILL.md) 把对话记录交给三个并行审查者。然后一个综合者把提议分成 `Accepted`、`Rejected` 和 `Backlog`，并在任何 skill 改动之前等你批准。只有会改变未来某个决定的提议才批准。一次古怪的会话是轶事，不是 rule。

## 写一个只做一件事的 skill

你已经知道要留下的工作流时：

```text
/poteto-mode write a skill for verifying database migrations in this repo
```

写 skill 走 [Authoring or modifying a skill playbook](../skills/poteto-mode/playbooks/authoring-a-skill.md)。它路由到 Cursor 内置的 `create-skill`，校验 frontmatter 和链接，再经 Opening a PR playbook 交付结果。面向代理的正文，门槛比给人读的正文更高。一句没用的话，会变成未来某个代理要遵循的指示。让 playbook 守住这个门槛。不要手写一份 `SKILL.md`。

有一种特例自带生成器。必须驱动你的应用并证明行为的 skill，是验证 skill。所以用 [`/create-verification-skill`](../skills/create-verification-skill/SKILL.md) 和 [`/maintain-verification-skill`](../skills/maintain-verification-skill/SKILL.md)。[验证结果并开 PR](./06-verify-and-ship.md#create-a-project-verification-skill) 两样都讲了。

## 用 `/technical-writing` 按标准写文档

skill 不是你交付的唯一正文。文档、RFC、readme、PR 描述和提交说明，用这个：

```text
/technical-writing review the readme changes
```

[`/technical-writing`](../skills/technical-writing/SKILL.md) 应用一套分层标准。目标只有一个：疲倦的工程师第一遍就能读懂。它先选定文档的模式（tutorial、how-to、reference 或 explanation），再逐句处理：谁做什么，一句一个意思，没有可以读成两种意思的句子。用它复审你或代理刚写的东西。要文档时，也可以一开始就点名它。

## 盲测 skill 改动

改一个 skill 会影响以后每一次会话。所以按实验的方式来测它：

```text
/poteto-mode run the eval playbook on this skill change. same task for both variants, candidates stay blind.
```

[Eval playbook](../skills/poteto-mode/playbooks/eval.md) 围绕一种失败模式来建：观察者效应。代理知道自己在被评估时，行为会不一样。所以候选代理拿到的是看起来很自然的任务，放在清理过的目录里。它们看不到「eval」或「candidate」这两个词，也不知道彼此存在。一个评判者在中性标签下给所有输出打分。是否顺着链读下去，按每个候选实际读了哪些文件来评分，不按它自己的声称。

接受 verdict 之前，自己读每一份输出。如果你不同意评判者，先怀疑评分标准，再怀疑你自己的判断。

**坑：** 不要因为 skill 表现不好，就在任务中途改它。在它自己的 PR 里修，任务继续往前。缠进功能工作里一起交付的 skill 改动，复审看不见，也无法评估。

下一篇：[配方与坑](./10-recipes-and-pitfalls.md)。
