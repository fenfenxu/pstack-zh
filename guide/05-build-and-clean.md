---
title: "构建并清理 diff"
description: "说出你观察到的，让构建 playbook 来要证据。这一页讲常见构建提示词，以及让 diff 可审的清理习惯。"
sourceUrl: "https://github.com/cursor/plugins/blob/12d587dfb20741cafc376c42c696c5f6e2a64487/pstack/docs/guide/05-build-and-clean.md"
---

> [!NOTE]
> **来源**
>
> 这是中文译文，方便对照阅读。本站不发行中文版插件。英文原文 [`pstack/docs/guide/05-build-and-clean.md`](https://github.com/cursor/plugins/blob/12d587dfb20741cafc376c42c696c5f6e2a64487/pstack/docs/guide/05-build-and-clean.md)，取自 pstack 官方仓库 cursor/plugins 的提交 `12d587d`。
>
> [本站英文页](https://pstack.droplink.cloud/en/skills-zh/official-guide/05-build-and-clean/)

# 构建这次改动，并清理 diff

构建类 playbook 共用一条纪律。说出你观察到的。让 playbook 来索取证据。这一页展示每种常见构建任务的提示词里该放什么，然后是让 diff 保持可审的清理习惯。

## 把你已经知道的告诉构建 playbook

一条 bug 提示词陈述症状，并要求先做复现：

```text
/poteto-mode this command emits two records after a retry. repro first, then fix and verify.
```

一条 feature 提示词陈述行为，以及什么必须不变：

```text
/poteto-mode add a --json flag. text output stays byte-identical. verify both forms.
```

一条重构提示词在结构移动之前钉住行为：

```text
/poteto-mode move parsing into one module, zero behavior change. record the current output first and prove it's unchanged after.
```

一条性能提示词陈述测量，不陈述感觉：

```text
/poteto-mode startup takes 1.8s on this fixture. trace it, fix the measured cause, show me before and after.
```

每一条都会路由到它的 playbook（[Bug fix](../skills/poteto-mode/playbooks/bug-fix.md)、[Feature](../skills/poteto-mode/playbooks/feature.md)、[Refactoring](../skills/poteto-mode/playbooks/refactoring.md)、[Perf issue](../skills/poteto-mode/playbooks/perf-issue.md)）。playbook 补上你没有打出来的步骤：修复前先复现，实现前先命名数据形状，重组前先钉住行为，优化前先剖析。

要对一个数字做持续改进，有 [Hillclimb playbook](../skills/poteto-mode/playbooks/hillclimb.md)。给它指标、目标，以及尝试次数的下限。它用一套冻结的测量 harness，一次循环一个假设。它保留赢的那些。其余全部回退。

## 用 `/tdd` 先写失败的测试

当一个 bug 有便宜的本地测试路径时，整条提示词可以只有两个词：

```text
/tdd implement
```

放在上下文里，这就够了。[`/tdd`](../skills/tdd/SKILL.md) 先写最小的测试，让它因为预期的原因失败，然后写修复，然后重跑测试。如果一个测试需要大范围搭建 harness，或需要脆弱的 mock，这个 skill 会说出来，并改用最接近的可执行检查。在真实命令是更强证据的地方，不要硬塞一个测试。

## 写 TypeScript 时自动带上规则

[`typescript-best-practices`](../skills/typescript-best-practices/SKILL.md) 在你的工作流里没有斜杠命令。只要 agent 碰到 `.ts` 或 `.tsx` 文件，它就会加载，并把类型系统的原则变成具体规则：可区分联合、边界上的 `unknown`、穷尽变体、由 schema 推导出的类型。

## 提交前先清理

[Opening a PR playbook](../skills/poteto-mode/playbooks/opening-a-pr.md) 在每次 commit 之前对 diff 跑 `/deslop`，并把 [`/unslop`](../skills/unslop/SKILL.md) 用到 PR 描述和 commit 正文上。`/deslop` 随 `cursor-team-kit` 插件提供，不在 pstack 里。如果你没有它，就用白话要求同样的结果：去掉叙述性注释、无依据的守卫、死掉的兼容路径，以及无关编辑。

对散文，`/unslop` 接受一个目标，以及你另外有的任何规则：

```text
/unslop the readme changes, no emdashes
```

你会发展出自己的简写。这个 skill 从 `unslop that, tighten it` 这种简短提示词里就能读懂意图。

## 用 `/no-comments` 清掉注释

注释需要单独过一遍，而且不能由写下它们的那个 agent 来过。作者捍卫自己的注释，方式就像你会捍卫你的注释。所以在 review 之前，把它们交给一双新的眼睛：

```text
/no-comments the diff
```

[`/no-comments`](../skills/no-comments/SKILL.md) 会拉起 [Comment Sicko](../agents/comment-sicko.md)。它只读。留下的很少：许可证头、公开 API 的文档注释、用来解释代码本身说不清的事的链接，以及外部依赖逼出来、你改不了的行为。其余都删掉。你自己代码里的意外，不在留下的范围里。注释如果其实是在标一块该重构的代码，会按这个处理。`/no-comments` 对它收下的标记，会到根因上去修。注释如果声称一条约束，比如「do not remove」，这个 skill 会提议把这条约束写成类型、测试或 lint。不管怎样，这条注释都会删掉。

这套分工值得记清楚。`/deslop` 把 slop 从代码里清掉，`/unslop` 把 slop 从散文里清掉，`/no-comments` 把注释交给一个没有写过它们的审查者。

**坑：** 清理不是可选的抛光。一份带着叙述性注释和防御性死重的 diff，在审查者看来是没做完的。多出来的代码，就是下一个 bug 藏身的地方。如果 diff 让人觉得被填胖了，在提交之前说 `deslop it`。不要等 review 把它点出来之后。

下一篇：[验证并交付](./06-verify-and-ship.md)。
