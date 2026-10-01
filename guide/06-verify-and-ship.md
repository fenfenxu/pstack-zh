---
title: "验证结果并开 PR"
description: "先写完结条件，再为应用生成验证 skill，开 PR，并把它推进到已合并。"
sourceUrl: "https://github.com/cursor/plugins/blob/12d587dfb20741cafc376c42c696c5f6e2a64487/pstack/docs/guide/06-verify-and-ship.md"
---

> [!NOTE]
> **来源**
>
> 这是中文译文，方便对照阅读。本站不发行中文版插件。英文原文 [`pstack/docs/guide/06-verify-and-ship.md`](https://github.com/cursor/plugins/blob/12d587dfb20741cafc376c42c696c5f6e2a64487/pstack/docs/guide/06-verify-and-ship.md)，取自 pstack 官方仓库 cursor/plugins 的提交 `12d587d`。
>
> [本站英文页](https://pstack.droplink.cloud/en/skills-zh/official-guide/06-verify-and-ship/)

# 验证结果并开 PR

「能编译」不是证据。[Prove It Works 原则](../skills/principle-prove-it-works/SKILL.md) 让代理在报告成功之前，先检查真实产物。你要做的，是让「真实产物」可以被检查。本页讲四件事：写明完结条件，为你的应用生成验证 skill，开 PR，再把它推进到已合并。

![原型飞机飞过真实试飞航线，她用秒表计时，机器人拍摄并用清单核对。终端显示 verify: pass, evidence: captured。](https://pstack.droplink.cloud/guide/verification.jpg)

## 一开始就写明完结条件

在第一条提示词里写明做完是什么意思。用你顺手的说法即可：

```text
/poteto-mode add json output to this command. text output stays byte-identical, the json parses, both run against the sample project. show me the evidence.
```

这样代理就有三项可以跑的检查。要满足的心情不算数。回复回来时，应带上确切的命令和输出。某项检查跑不了，好的回复会说「inconclusive」。没有证据却很有信心的回复，你要当成危险信号。

让检查和改动对上：

- CLI 改动，就跑真实命令。
- UI 改动，就在正在运行的应用里走一遍改过的流程。
- 解析器或迁移，就重放一份保存好的输入。
- 性能改动，就对比前后的 profile。
- 存储改动，就把写下的值读回来。

对一份你不完全信任的小 diff，[`/blast-radius`](../skills/blast-radius/SKILL.md) 找出它还可能在别处弄坏什么。它挑出改动之所以安全所靠的那一个事实，并用跑代码来证明。不写一篇长文来论证。

<a id="create-a-project-verification-skill"></a>

## 为项目创建验证 skill

上面那条 UI 要点背后有个真实要求。代理需要一套脚本化的办法来驱动你的应用。项目里已经有，就用现成的。没有就运行：

```text
/create-verification-skill
```

[`/create-verification-skill`](../skills/create-verification-skill/SKILL.md) 问的是仓库，不是你。它弄清用户会碰到什么，应用如何在本地启动，用什么来驱动（先用已有的 harness，否则用浏览器和 CDP、PTY，或普通 HTTP），什么证据能证明行为，以及两个实例能否并排运行。它只问你代码回答不了的事。

它写入 `.cursor/skills/verify-<app>/`。里面是面向代理的说明，带有确切的 Launch、Doctor、Drive、Evidence、Cleanup 各节。`features/` 下还有一份 feature map，索引应用做了什么，以及什么结果能证明每个功能有效。这个 skill 自带一份[做好的 feature map 示例](../skills/create-verification-skill/references/feature-map-example/README.md)，含一份 README 索引，每个功能一个文件，用上四个必需的 H2。交给你之前，生成器会把这个 skill 端到端证明一次：启动，doctor 检查，驱动一个功能，采集证据，清理。这次证明失败，就不要用这份输出。

从此以后，「在应用里验证」是任何代理都能执行的一步。就在这个仓库里。不用再开一场配置对话。

验证 skill 能用之后，[`/swarm`](../skills/swarm/SKILL.md) 可以按 feature map 条目拆开一整轮，再把结果汇总起来。

## 让验证 skill 保持真实

应用会变，feature map 会过期。你的这份漂了，就运行：

```text
/maintain-verification-skill
```

[`/maintain-verification-skill`](../skills/maintain-verification-skill/SKILL.md) 审计生成出来的 skill。每个功能一个只读的源码阅读者，并行进行。然后做一轮现场通过，驱动每一个已映射的功能。结束时正好是三种结果之一。`clean` 表示覆盖完整，没有要交付的东西。`changed` 表示一个已证明的修正 PR，范围限在验证 skill 自己的目录里。`blocked` 会点出阻塞项。它从不改产品代码。现场通过如果抓到产品回归，它报告这个回归。不在文档里把它糊过去。

## 开 PR

```text
/poteto-mode open the pr. small ordered commits, evidence in the description.
```

[Opening a PR playbook](../skills/poteto-mode/playbooks/opening-a-pr.md) 从 worktree 开工。它把工作变基成小而有序的提交，清理 diff，unslop 正文，并返回 PR 链接。五个窄 PR 胜过一个肥 PR。后续改动叠加上去，也好过让一个分支越长越大。

## 用 Babysit 把 PR 推到可合并

PR 一开，阻塞项马上开始堆积。检查会失败。审查者会评论。主干会动。把这摊折腾交给 [Babysit playbook](../skills/poteto-mode/playbooks/babysit.md)：

```text
/poteto-mode babysit this pr. get it green.
```

Babysit 用自带的 watcher 盯着 PR，并按顺序处理阻塞项：先冲突，再审查线程，然后是 CI。已知的修复攒进同一次 push。检查只重启一次，不用每修一处就重启。评论分拣持怀疑态度。人和机器人把真实捕获和噪声放在同一张列表里。真实的 finding 会得到修复。噪声会被驳回，反证贴在线程上。如果你只要状态，就把请求说小一点。Babysit 会回答，不会启动那一轮循环：

```text
/poteto-mode check on pr 123. anything outstanding?
```

Babysit 停在可合并。即使全部变绿，它也从不合并。合并是另一个决定。

## 用 Shipping 把栈落地

全绿和安全是两回事。你准备落地时，直接说：

```text
/poteto-mode land the stack.
```

[Shipping playbook](../skills/poteto-mode/playbooks/shipping.md) 在启动任何动作之前，先独立验证每个 PR。每个 PR 由一个新的代理现场证明行为。评判改动的代理，永远不是写下它的那个。然后 Shipping 只从底部落地连续已验证的那一段。一次一个 PR。默认经 GitHub。Origin 的 CLI 可用时走 Origin。它会报告打断这条链的第一个 PR。一个已验证的 PR 如果坐在未验证的 PR 上面，就等待。合并它会把下面的缺口卷进来。

下一篇：[睡觉时让工作继续跑](./07-overnight.md)。
