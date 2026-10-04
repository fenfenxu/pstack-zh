---
title: "pstack 指南"
description: "别再一步一步地指挥 agent。说清你要什么、怎样算做完，/poteto-mode 会挑 playbook、调用其他 skills，再把证据拿给你看。"
sourceUrl: "https://github.com/cursor/plugins/blob/e43c7ee26e0038c6c1fa8380dd34ce86ff94cb2a/pstack/docs/guide/README.md"
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
> 对照英文：[本站英文页](https://pstack.ganhai.cloud/en/skills-zh/official-guide/)
>
> 英文原文出处：cursor/plugins 仓库 [`pstack/docs/guide/README.md`](https://github.com/cursor/plugins/blob/e43c7ee26e0038c6c1fa8380dd34ce86ff94cb2a/pstack/docs/guide/README.md)（提交 `e43c7ee`）

# pstack 指南

别再一步一步地指挥 agent，pstack 才最好用。你说清想要什么，以及怎样算做完。`/poteto-mode` 负责挑 playbook，按步骤需要调用其他 skills，最后把证据拿给你看。这份指南用贴近实际的提示词，带你养成这个习惯。

你会学到这些：

1. [安装 pstack](./01-setup.md)。装好插件，选好模型。
2. [把工作交给 `/poteto-mode`](./02-poteto-mode.md)。给它一个目标，看它怎么挑 playbook。
3. [理解代码](./03-understand.md)。动手改之前，先用 `/how`、`/why`、`/teach` 和 `/recall`。
4. [设计这次改动](./04-design.md)。代码定型之前，先用 `/architect`、`/arena`、`/swarm` 和 `/interrogate`。
5. [构建并清理这次改动](./05-build-and-clean.md)。构建类 playbook，以及 `/tdd`、`/unslop` 和 `/no-comments`。
6. [验证并交付](./06-verify-and-ship.md)。先在真实应用上证明行为没问题，再开一个只做一件事的 PR，一路推到合并。
7. [睡觉时让工作继续跑](./07-overnight.md)。一份过夜交接约定、一份可以审计的决策日志，以及能扩展到多个 agent 的 playbook。
8. [用原则名来转向](./08-principles.md)。这 24 个名字能在任务中途让 agent 改方向。
9. [把它变成你的](./09-make-it-yours.md)。做一个你自己的 mode，再学会测试 skill 改动。
10. [配方与坑](./10-recipes-and-pitfalls.md)。可以照抄的提示词，以及不用再犯的错。

第一次读，按顺序来。之后每一页都可以单独看。

## 如果你只记住一件事

用你自己的话，给 agent 一个目标，再给它一个检查办法：

```text
/poteto-mode the export writes duplicate rows when a retry lands mid-run. repro first, then fix and verify.
```

不用点名 playbook，也不用列出 skills。`/poteto-mode` 只凭「repro first」（先复现）和一个能检查的结果，就能判断该走哪个 playbook。它会匹配到 Bug fix playbook，把步骤抄进 todo 列表，每走到一步就调用该用的 skills。

下一篇：[安装 pstack](./01-setup.md)。
