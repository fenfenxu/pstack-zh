---
title: "构建并清理 diff"
description: "你说清看到了什么，证据由构建类 playbook 去要。本页讲常见构建任务的提示词怎么写，以及让 diff 好审的清理习惯。"
sourceUrl: "https://github.com/cursor/plugins/blob/e43c7ee26e0038c6c1fa8380dd34ce86ff94cb2a/pstack/docs/guide/05-build-and-clean.md"
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
> 对照英文：[本站英文页](https://pstack.ganhai.cloud/en/skills-zh/official-guide/05-build-and-clean/)
>
> 英文原文出处：cursor/plugins 仓库 [`pstack/docs/guide/05-build-and-clean.md`](https://github.com/cursor/plugins/blob/e43c7ee26e0038c6c1fa8380dd34ce86ff94cb2a/pstack/docs/guide/05-build-and-clean.md)（提交 `e43c7ee`）

# 构建这次改动，并清理 diff

构建类 playbook 都守同一条规矩。你说清自己看到了什么，证据由 playbook 去要。本页先讲每种常见构建任务的提示词里该写什么，再讲一个让 diff 保持好审的清理习惯。

## 把你已经知道的告诉构建 playbook

修 bug 的提示词写清症状，并要求先复现：

```text
/poteto-mode this command emits two records after a retry. repro first, then fix and verify.
```

加功能的提示词写清要什么行为，以及哪些东西不能变：

```text
/poteto-mode add a --json flag. text output stays byte-identical. verify both forms.
```

重构的提示词在动结构之前，先把现有行为固定下来：

```text
/poteto-mode move parsing into one module, zero behavior change. record the current output first and prove it's unchanged after.
```

性能优化的提示词写测量结果，不写感觉：

```text
/poteto-mode startup takes 1.8s on this fixture. trace it, fix the measured cause, show me before and after.
```

这几条提示词各自会交给对应的 playbook（[Bug fix](../skills/poteto-mode/playbooks/bug-fix.md)、[Feature](../skills/poteto-mode/playbooks/feature.md)、[Refactoring](../skills/poteto-mode/playbooks/refactoring.md)、[Perf issue](../skills/poteto-mode/playbooks/perf-issue.md)）。你没写出来的步骤，playbook 会补上：修之前先复现，实现之前先说清数据形状，重组之前先固定行为，优化之前先剖析性能。

要让某一个数字持续变好，就用 [Hillclimb playbook](../skills/poteto-mode/playbooks/hillclimb.md)。告诉它指标、目标，以及最少要试多少次。它一轮只试一个假设，测量用的 harness（包在被测代码外面，负责运行和测量的程序）全程不动。有用的改动留下，其余全部撤回。

## 用 `/tdd` 先写失败的测试

如果一个 bug 在本地很容易测，整条提示词可以只有两个词：

```text
/tdd implement
```

有对话里的上下文，这就够了。[`/tdd`](../skills/tdd/SKILL.md) 先写一个最小的测试，让它因为预期的原因失败，接着写修复，再重跑这个测试。如果写测试要先搭一大套 harness，或者得靠脆弱的 mock，这个 skill 会直接说明，改用最接近的可执行检查。真实命令能给出更强的证据时，别硬写测试。

## 写 TypeScript 时自动带上规则

[`typescript-best-practices`](../skills/typescript-best-practices/SKILL.md) 用不着你敲斜杠命令。agent 一碰 `.ts` 或 `.tsx` 文件，它就自动加载，把类型系统的原则落成具体规则：可辨识联合、边界处用 `unknown`、穷尽所有变体、从 schema 推导类型。

## 提交前先清理

[Opening a PR playbook](../skills/poteto-mode/playbooks/opening-a-pr.md) 每次提交前都会对 diff 跑一遍 `/deslop`，并用 [`/unslop`](../skills/unslop/SKILL.md) 处理 PR 描述和 commit 说明。`/deslop` 属于 `cursor-team-kit` 插件，不在 pstack 里。没有装的话，就用大白话提出同样的要求：删掉复述代码的注释、没有依据的防御检查、已经没人走的兼容分支，以及跟这次改动无关的修改。

处理文字时，`/unslop` 接收一个目标，再加上你自己的额外规则：

```text
/unslop the readme changes, no emdashes
```

用久了，你会有自己的简写。像 `unslop that, tighten it` 这样很短的提示词，这个 skill 也能读懂意图。

## 用 `/no-comments` 清掉注释

注释要单独过一遍，而且不能让写注释的那个 agent 来过。作者会护着自己写的注释，就像你也会护着你的。所以在审查之前，换一双眼睛来看：

```text
/no-comments the diff
```

[`/no-comments`](../skills/no-comments/SKILL.md) 会启动 [Comment Sicko](../agents/comment-sicko.md)。它是只读的审查者，允许留下的注释只有一张很短的清单：许可证头、公开 API 的文档注释、解释代码说不清之处的链接，以及外部依赖强加、你又改不了的行为。其余一律删掉。你自己代码里的反常之处没有这种豁免。这类注释会作为重构信号报回来，`/no-comments` 认可哪一条，就从根因上修哪一条。如果注释声称有某种约束，比如「do not remove」，这个 skill 会提议把这条约束写成类型、测试或 lint。约束写没写成代码，注释都要删。

这几样工具的分工要记清。`/deslop` 清掉代码里的 slop（AI 写出来的冗余、空洞内容），`/unslop` 清掉文字里的 slop，`/no-comments` 把注释交给一个没写过它们的审查者。

**坑：** 清理不是可做可不做的润色。diff 里留着复述代码的注释和防御性的累赘，审查者看了会觉得活没干完。多出来的代码，正是下一个 bug 藏身的地方。diff 要是看着注了水，提交前就说 `deslop it`，别等审查指出来。

下一篇：[验证并交付](./06-verify-and-ship.md)。
