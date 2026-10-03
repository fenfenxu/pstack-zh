---
title: "验证结果并开 PR"
description: "能编译不等于有证据。本页讲怎么写明完结条件、为应用生成验证 skill、开 PR，再把 PR 一路推到合并。"
sourceUrl: "https://github.com/cursor/plugins/blob/23e4138daa01c42d4969f7a5465f82704e64f798/pstack/docs/guide/06-verify-and-ship.md"
---

> [!NOTE]
> **来源**
>
> 这是中文译文，方便对照阅读。本站不发行中文版插件。
>
> 对照英文：[本站英文页](https://pstack.ganhai.cloud/en/skills-zh/official-guide/06-verify-and-ship/)
>
> 英文原文出处：cursor/plugins 仓库 [`pstack/docs/guide/06-verify-and-ship.md`](https://github.com/cursor/plugins/blob/23e4138daa01c42d4969f7a5465f82704e64f798/pstack/docs/guide/06-verify-and-ship.md)（提交 `23e4138`）

# 验证结果并开 PR

「能编译」不是证据。[Prove It Works 原则](../skills/principle-prove-it-works/SKILL.md) 要求 agent 先检查真实产物，再报告成功。你的任务，是让这个「真实产物」有办法检查。本页讲四件事：写明完结条件，为你的应用生成验证 skill，开 PR，再把 PR 一路推到合并。

![一架原型机在真实的试飞航线上飞行。她拿着秒表计时，几个机器人在拍摄，并对照清单逐项核对这次试飞。终端上显示 verify: pass, evidence: captured。](https://pstack.ganhai.cloud/guide/verification.jpg)

## 一开始就写明完结条件

在第一条提示词里就写明怎样才算做完，用什么说法都行：

```text
/poteto-mode add json output to this command. text output stays byte-identical, the json parses, both run against the sample project. show me the evidence.
```

这样 agent 手里有了三项能跑的检查，不用再去猜你满不满意。agent 回复时，应该附上它跑过的确切命令和输出。某项检查跑不了，好的回复会写明「inconclusive」（无法下结论）。如果回复说得很笃定，却拿不出证据，你要把它当成危险信号。

检查要跟改动对得上：

- CLI 改动，就跑真实的命令。
- UI 改动，就在运行中的应用里把改过的流程走一遍。
- 解析器或迁移，就拿一份存好的输入重放一遍。
- 性能改动，就对比改动前后的性能剖析。
- 存储改动，就把写进去的值读回来。

如果有一份小 diff 你不完全放心，[`/blast-radius`](../skills/blast-radius/SKILL.md) 会找出它可能在别处弄坏什么。它挑出让这次改动安全的那一个事实，然后跑代码证明它，而不是写一篇长文来论证。

<a id="create-a-project-verification-skill"></a>

## 为项目创建验证 skill

上面讲 UI 的那一条，背后藏着一个实际的要求。agent 需要一种能用脚本操作你应用的办法。项目里已经有了，那最好。没有的话，运行：

```text
/create-verification-skill
```

[`/create-verification-skill`](../skills/create-verification-skill/SKILL.md) 找仓库要答案，不找你。它要弄清这几件事：用户会碰到哪些地方，应用在本地怎么启动，用什么来操作它，什么证据能证明行为，两个实例能不能同时跑。操作手段优先用项目里现成的 harness（用来启动和操作应用的测试程序），没有的话，再用浏览器加 CDP、PTY（伪终端），或者直接发 HTTP 请求。只有代码答不出来的问题，它才来问你。

它会写出 `.cursor/skills/verify-<app>/`。里面是写给 agent 看的说明，分成 Launch、Doctor、Drive、Evidence、Cleanup 几节，每节都写得很具体。`features/` 下面还有一份 feature map，列出应用的各项功能，以及每项功能用什么结果来证明可用。这个 skill 自带一份[完整的 feature map 示例](../skills/create-verification-skill/references/feature-map-example/README.md)，包括一个 README 索引，每个功能一个文件，每个文件都用规定的四个二级标题。交给你之前，生成器会把这个 skill 从头到尾跑通一次：启动，doctor 检查，操作一个功能，采集证据，清理。这一遍没跑通，就别用它生成的东西。

从此以后，在这个仓库里，「在应用里验证一下」就成了任何 agent 都能执行的一步，不用先聊一轮怎么配置。

验证 skill 能用以后，可以让 [`/swarm`](../skills/swarm/SKILL.md) 按 feature map 的条目把一整轮验证拆开，再汇总结果。

## 让验证 skill 跟上应用

应用会变，feature map 会过时。你的这份跟不上了，就运行：

```text
/maintain-verification-skill
```

[`/maintain-verification-skill`](../skills/maintain-verification-skill/SKILL.md) 会审查生成出来的 skill。它先按功能拆开，每个功能一路，并行读源码，全程只读不写。然后实际跑一轮，把 feature map 里的每个功能都操作一遍。最后的结果一定是下面三种之一。`clean` 表示全部覆盖到了，没有东西要提交。`changed` 表示有一个 PR，里面是验证过的修正，改动只限于验证 skill 自己的目录。`blocked` 会写明卡在哪里。它从不改产品代码。实际跑的那一轮如果发现产品回归，它会报告这个回归，不会在文档里把它糊弄过去。

## 开 PR

```text
/poteto-mode open the pr. small ordered commits, evidence in the description.
```

[Opening a PR playbook](../skills/poteto-mode/playbooks/opening-a-pr.md) 在 worktree（同一个仓库的另一份独立工作目录）里干活。它用 rebase 把改动整理成几个有序的小提交，清理 diff，再用 unslop 去掉文字里的 AI 腔，最后返回 PR 链接。五个范围窄的 PR 胜过一个臃肿的大 PR。把后续改动叠成新的 PR，也胜过让一个分支越长越大。

## 用 Babysit 把 PR 推到可合并

PR 一开，挡住合并的问题马上开始堆积。检查会挂，审查者会留评论，主干也在往前走。把这些来回折腾交给 [Babysit playbook](../skills/poteto-mode/playbooks/babysit.md)：

```text
/poteto-mode babysit this pr. get it green.
```

Babysit 用自带的监视脚本盯着 PR，按顺序处理问题：先解决冲突，再处理审查评论，最后是 CI。已知的修复会攒在一起，一次推送上去。这样检查只重跑一次，不用每修一处就重跑一遍。它筛评论时抱着怀疑的态度，因为人和机器人都会把真问题和噪声提在同一张列表里。真的 finding（审查中发现的问题）就修。噪声就驳回，并把反驳的证据贴在那条评论下面。如果你只想知道现状，就问得小一点。Babysit 会直接回答，不会启动那套循环：

```text
/poteto-mode check on pr 123. anything outstanding?
```

Babysit 推到可合并就停。哪怕全绿，它也从不合并，因为合并是另一个决定。

## 用 Shipping 合并整叠 PR

全绿不等于安全。准备好合并时，直接说：

```text
/poteto-mode land the stack.
```

[Shipping playbook](../skills/poteto-mode/playbooks/shipping.md) 在给任何 PR 开启合并之前，先逐个独立验证。每个 PR 都由一个新开的 agent 实地证明行为，评判改动的 agent 永远不是写这个改动的那一个。然后 Shipping 从最底下开始，只合并连续通过验证的那一段。它一次合并一个 PR，默认走 GitHub，Origin 的 CLI 可用时走 Origin，最后报告链条断在哪个 PR。如果一个通过验证的 PR 压在一个没验证的 PR 上面，它就得等。现在合并它，会把底下那个没验证的缺口一起带进去。

下一篇：[睡觉时让工作继续跑](./07-overnight.md)。
