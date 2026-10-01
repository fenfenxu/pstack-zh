---
title: "pstack 指南"
description: "说出目标，以及你怎么知道做完了。/poteto-mode 选 playbook、运行 skills，并把证据给你看。"
sourceUrl: "https://github.com/cursor/plugins/blob/12d587dfb20741cafc376c42c696c5f6e2a64487/pstack/docs/guide/README.md"
---

> [!NOTE]
> **来源**
>
> 这是中文译文，方便对照阅读。本站不发行中文版插件。英文原文 [`pstack/docs/guide/README.md`](https://github.com/cursor/plugins/blob/12d587dfb20741cafc376c42c696c5f6e2a64487/pstack/docs/guide/README.md)，取自 pstack 官方仓库 cursor/plugins 的提交 `12d587d`。
>
> [本站英文页](https://pstack.droplink.cloud/en/skills-zh/official-guide/)

# pstack 指南

pstack 在你不再微操 agent 时效果最好。你描述想要什么，以及你怎么知道做完了。`/poteto-mode` 选出 playbook，在步骤需要时运行其他 skills，并把证据给你看。本指南用贴近真实的提示词教这个习惯。

你会学到这些：

1. [安装 pstack](./01-setup.md)。安装插件，并选定你的模型。
2. [把工作交给 `/poteto-mode`](./02-poteto-mode.md)。给它一个目标，看它选出 playbook。
3. [理解代码](./03-understand.md)。改任何东西之前，先用 `/how`、`/why`、`/teach` 和 `/recall`。
4. [设计这次改动](./04-design.md)。代码锁死形状之前，先用 `/architect`、`/arena`、`/swarm` 和 `/interrogate`。
5. [构建并清理这次改动](./05-build-and-clean.md)。构建类 playbooks、`/tdd`、`/unslop` 和 `/no-comments`。
6. [验证并交付](./06-verify-and-ship.md)。在真实应用上证明行为，然后开一个聚焦的 PR，并把它推到合并。
7. [你睡觉时让工作继续跑](./07-overnight.md)。过夜之前要说清的几件事、一份可以审计的决策日志，以及规模能超过单个 agent 的 playbooks。
8. [用原则名来转向](./08-principles.md)。任务中途用来给 agent 改道的 23 个名字。
9. [把它变成你的](./09-make-it-yours.md)。你自己的 mode，加上怎么测试一次 skill 改动。
10. [配方与坑](./10-recipes-and-pitfalls.md)。可以照抄的提示词，以及该跳过的错误。

第一次按顺序读这些页。之后每一页都能单独成立。

## 如果你只记住一件事

用你自己的话，给 agent 一个目标，以及一种检查它的办法：

```text
/poteto-mode the export writes duplicate rows when a retry lands mid-run. repro first, then fix and verify.
```

你不需要点名 playbook，也不需要列出 skills。「repro first」和一个可检查的结果，就是 `/poteto-mode` 需要的全部路由信号。它匹配 Bug fix playbook，把步骤抄进 todo 列表，并在每一步触发时调用对应的 skills。

下一篇：[安装 pstack](./01-setup.md)。
