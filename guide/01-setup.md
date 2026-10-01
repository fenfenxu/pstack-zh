---
title: "安装 pstack"
description: "安装插件，选定 pstack 用的模型，并跑第一个任务。安装是一条命令，加上一段短对话。"
sourceUrl: "https://github.com/cursor/plugins/blob/12d587dfb20741cafc376c42c696c5f6e2a64487/pstack/docs/guide/01-setup.md"
---

> [!NOTE]
> **来源**
>
> 这是中文译文，方便对照阅读。本站不发行中文版插件。英文原文 [`pstack/docs/guide/01-setup.md`](https://github.com/cursor/plugins/blob/12d587dfb20741cafc376c42c696c5f6e2a64487/pstack/docs/guide/01-setup.md)，取自 pstack 官方仓库 cursor/plugins 的提交 `12d587d`。
>
> [本站英文页](https://pstack.droplink.cloud/en/skills-zh/official-guide/01-setup/)

# 安装 pstack

这一页你安装插件，选定 pstack 用哪些模型，并跑第一个任务。安装是一条命令，加上一段短对话。

## 安装插件

在 Cursor 聊天里运行：

```text
/add-plugin pstack
```

Cursor 确认插件已安装。

## 选定你的模型

运行：

```text
/setup-pstack
```

[`/setup-pstack`](../skills/setup-pstack/SKILL.md) 会看看你能用哪些模型，问你推理预算，把每个角色列出来（代码 delegate、判断、review 面板），再问你要哪一个。你回答完，它把选择写进 `~/.cursor/rules/pstack-models.mdc`。这是一条小 rule，每个 pstack skill 都会读。

你只改你在意的角色。某个角色在 rule 里没有那一行，就用 skill 自己的默认。想恢复默认，删掉该角色的那一行。再跑一次 `/setup-pstack` 时，模型和默认不一样的角色会留下来。0.15.3 之前写的 rule 锁的是旧默认模型。删掉那些角色行，或者删掉整个文件，然后再跑 `/setup-pstack`。

你可能会问，选 Auto 会怎样。把某个角色设成 `inherit-parent` 或 `auto`，pstack 就不写子代理的 `model` 字段。子代理接着用父聊天的模型。这两个值意思一样，也都不是模型 slug。面板角色的值是一份列表。列表里每一项跑一个子代理，所以列表有多长，面板就有多大。安装还会配置 `swarm workers`，也就是每个 `/swarm` worker 的默认模型。某次 race 如果给每条 arm 单独指定了模型，就用那次指定的。

## 创建你的验证 skill

安装快结束时，`/setup-pstack` 会在项目里找现成的办法，用来证明应用的行为。它找 `verify-*` skill，也找已有的 harness。两个都没有，它会问你一次：要不要用 [`/create-verification-skill`](../skills/create-verification-skill/SKILL.md) 生成一个。

回答 yes，它把 skill 写到 `.cursor/skills/verify-<app>/`。这个 skill 只属于当前项目，教 agent 像用户一样操作你的应用。它会先自己跑通一次，再交给你。回答 no，安装继续。你以后随时可以自己跑 `/create-verification-skill`。[验证并交付](./06-verify-and-ship.md#create-a-project-verification-skill) 说明什么时候该做这一步。

装完之后，新开一个聊天。模型 rule 只对新会话生效。

## 跑你的第一个任务

挑一件真实但小的事，用你跟同事描述的方式描述它：

```text
/poteto-mode add a --json flag to this command. text output stays byte-identical. verify both.
```

看 todo 列表。最前面几项是匹配到的 playbook 的步骤，原样抄进来。对这个提示词，那就是 Feature playbook。如果 `/poteto-mode` 跳过一步，这一步仍留在列表里，并带 `skip: <reason>`。这样你能看见它选择不做的事。

从这里起，你可以直接接着说。`/poteto-mode` 会一直开着，直到你说要退出。

下一篇：[把工作交给 `/poteto-mode`](./02-poteto-mode.md)。
