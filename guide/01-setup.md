---
title: "安装 pstack"
description: "这一页装好插件，选定 pstack 用的模型，再跑第一个任务。安装只要一条命令，加一段简短的对话。"
sourceUrl: "https://github.com/cursor/plugins/blob/d73344bee8cf22e53b9d5f4cf5749d38ba38c174/pstack/docs/guide/01-setup.md"
meta:
  updated_at: "2026-10-10T13:49:04+08:00"
  updated_by: "cursor-cloud-agent grok-4.6"
  triggered_by: "liu xu"
  translation:
    model: "grok-4.6"
    effort: "high"
    translated_at: "2026-10-10T13:49:04+08:00"
    source_version: "0.15.15 / d73344b"
---

> [!NOTE]
> **来源**
>
> 这是中文译文，方便对照阅读。本站不发行中文版插件。
>
> 对照英文：[本站英文页](https://pstack.ganhai.cloud/en/skills-zh/official-guide/01-setup/)
>
> 英文原文出处：cursor/plugins 仓库 [`pstack/docs/guide/01-setup.md`](https://github.com/cursor/plugins/blob/d73344bee8cf22e53b9d5f4cf5749d38ba38c174/pstack/docs/guide/01-setup.md)（提交 `d73344b`）

# 安装 pstack

这一页里，你会装好插件，选定 pstack 用哪些模型，再跑第一个任务。安装只要一条命令，加上一段简短的对话。

## 安装插件

在 Cursor 的聊天里运行：

```text
/add-plugin pstack
```

Cursor 会告诉你插件已经装好。

## 选定你的模型

运行：

```text
/setup-pstack
```

[`/setup-pstack`](../skills/setup-pstack/SKILL.md) 会找出你能用的模型，问你推理预算定多少，再把每个角色列给你看（写代码的子代理、做判断的模型、审查用的几个模型组），问你想怎么配。回答这些问题。然后它写出 `~/.cursor/rules/pstack-models.mdc`。这是一条很短的 rule，每个 pstack skill 都会读它。

默认按 `xhigh` 推理跑，和 `large` 预算一样。`unlimited` 把每个模型升到它最高的一档，最高到 `max`。Opus 升到 `max`。Grok 最高只有 `xhigh`，所以停在那里。`medium` 和 `small` 会降低推理强度，少花 token。

你只改自己在意的部分。rule 里没写的角色，沿用 skill 自带的默认值。想恢复默认，就删掉那个角色的那一行。重新跑 `/setup-pstack` 时，模型和默认值不一样的角色会原样保留。默认值一变，变之前写出的 rule 仍把旧默认写死。所以要删掉那些角色行，或者直接删掉整个文件，然后重新跑一次 `/setup-pstack`。

你可能会问，用 Auto 的话怎么办。把角色设成 `inherit-parent` 或 `auto`，pstack 就不给子代理填 `model` 字段，子代理会沿用父聊天的模型。这两个值是一个意思，也都不是模型 slug。模型组角色的值是一个列表。列表里每一项跑一个子代理，所以列表有几项，这一组就有几个子代理。安装还会配置 `swarm workers`，也就是每个 `/swarm` worker 默认用的模型。只有当某次 race（几个 worker 做同一件事，按规则选出结果）给每个 arm（race 里的每一路）都指定了模型，才不用这个默认值。

## 创建你的验证 skill

安装的最后，`/setup-pstack` 会在你的项目里找一种证明应用行为的办法。它找的是 `verify-*` skill，或者现成的 harness（能驱动应用、检查结果的测试工具）。两样都没有，它会主动问你一次，要不要用 [`/create-verification-skill`](../skills/create-verification-skill/SKILL.md) 生成一个。

回答 yes，它把 skill 写到 `.cursor/skills/verify-<app>/`。这个 skill 只属于当前项目，教 agent 像用户一样操作应用。交给你之前，它会先跑通一次，证明这个 skill 能用。回答 no，安装就接着往下走。你随时可以自己跑 `/create-verification-skill`。细节见[验证并交付](./06-verify-and-ship.md#create-a-project-verification-skill)。

刚接触 pstack，就回答 yes。能自己检查工作的 agent，会一直做到检查通过。做不到的 agent，每交出一份结果都得你亲手核对。这份指南里，验证 skill 回报最大。

装完以后，新开一个聊天。模型 rule 从新会话开始生效。

## 把花销压住

pstack 在子代理和审查组上多花 token。严谨就是这个价钱。想少花：

- 再跑一次 `/setup-pstack`，选更小的推理预算，或更便宜的模型。主聊天用强模型，写代码的角色用更便宜、更快的模型，这样分比较好。
- 把某个角色设成 `auto` 或 `inherit-parent`，让它跑在聊天自己的模型上。
- 缩短模型组列表。每一项都会开一个子代理。
- 把 `/poteto-mode` 留给需要严谨的活。又小又一眼能看懂的改动，用不着。

<a id="run-your-first-task"></a>
## 跑你的第一个任务

挑一件真实的小事，像跟同事交代那样把它说清楚：

```text
/poteto-mode add a --json flag to this command. text output stays byte-identical. verify both.
```

留意 todo 列表。排在最前面的几项，是从匹配到的 playbook 原样抄进来的步骤。这条提示词匹配到的是 Feature playbook。`/poteto-mode` 如果跳过某一步，这一步仍然留在列表里，标着 `skip: <reason>`。这样你能看到它决定不做哪些事。

之后你照常接着说就行。想让 `/poteto-mode` 整段聊天都在，从 `/` 菜单里选它时，用 Option+Enter（Mac）或 Alt+Enter（Windows），不要用 Enter。这样它会变成 [Custom Mode](https://cursor.com/docs/skills)，每一轮都留在上下文里，直到你退出。Custom Mode 在 Agents Window 和 CLI 里都能用。普通的 Enter 只把这个 skill 挂到这一条消息上，聊天往前走，它就会淡掉。

下一篇：[把工作交给 `/poteto-mode`](./02-poteto-mode.md)。
