---
title: "pstack 插件说明"
description: "pstack 是 poteto 每天在 Cursor 交付高质量代码时用的那套 skill。它把 Cursor 变成一支真正的工程团队，帮你少写代码、写好代码。"
sourceUrl: "https://github.com/cursor/plugins/blob/e43c7ee26e0038c6c1fa8380dd34ce86ff94cb2a/pstack/README.md"
meta:
  updated_at: "2026-10-04T09:34:38+08:00"
  updated_by: "cursor-cloud-agent cursor"
  triggered_by: "pstack-daily-translate routine"
---

> [!NOTE]
> **来源**
>
> 这是中文译文，方便对照阅读。本站不发行中文版插件。
>
> 对照英文：[本站英文页](https://pstack.ganhai.cloud/en/skills-zh/official-guide/plugin-readme/)
>
> 英文原文出处：cursor/plugins 仓库 [`pstack/README.md`](https://github.com/cursor/plugins/blob/e43c7ee26e0038c6c1fa8380dd34ce86ff94cb2a/pstack/README.md)（提交 `e43c7ee`）

# pstack

我是 [poteto](https://x.com/poteto)。我不是什么总裁，也不是 CEO，但我在 Meta、Netflix 和 Cursor 经手过几百万行代码。我还在 React 核心团队，参与开发和维护 React Compiler。

越来越多人觉得，AI 写了太多 slop（粗制滥造）代码。我同意。我可不想像二十个 slop 写手凑成的团队那样交付。光有产量、没有质量，不是我想追求的。想走得快，先走得深。

**pstack 就是我的答案。** 我每天在 Cursor 交付高质量代码，用的就是这些 skill。它把 Cursor 变成一支真正的工程团队。目标不是让代码行数越多越好，恰恰相反。pstack 帮你少写代码，写出来的质量却更高。

**pstack 让你放心大胆地并行。** 你能让一个 agent 钻得够深，信得过它写出好的、可验证的代码，才能真正有底气地并行。用 `poteto-mode` 启动多个 agent，相信它们会用严格的工程原则做事。

**Cursor 让你兼得各家之长。** 每个 frontier（最前沿）模型都有长处，也有短板。pstack 配哪个模型都能用。其实，我的很多 skill 都用多模型工作流，好发挥每个模型独有的长处。

fork 它。改进它。把它变成你自己的。欢迎提 PR！

## 安装

```bash
/add-plugin pstack
```

## 开始

两步：

1. 运行 [`/setup-pstack`](../skills/setup-pstack/SKILL.md)，选一个推理预算，再挑好你要用的模型。
2. 只要手上的事需要严谨，就用 [`/poteto-mode`](../skills/poteto-mode/SKILL.md)。

刚接触 pstack？[pstack 指南](./index.md)会带你做完第一个真实任务，从安装配置、写提示词，一直到验证和过夜运行。

就这些。其他 skill 看场合用，这个 mode skill 会在需要时替你调用。默认配置下，它按模型的长处分活：写代码的子代理（feature、refactoring、bug fix、perf、hillclimb）用 grok，最难的改动、文字和判断用 opus 5.5。默认的评审团是 opus 5.5 / sol / grok。这些都能用 [`/setup-pstack`](../skills/setup-pstack/SKILL.md) 改。

## 用法

任务一开始就用 [`/poteto-mode`](../skills/poteto-mode/SKILL.md)。它会读你的请求，从一组 playbook（针对某类任务写好的一套步骤）里挑一个，再在步骤需要时运行其他 skill。

### 就用 [`/poteto-mode`](../skills/poteto-mode/SKILL.md)

这个 skill 是最主要的捷径。只要我需要 agent 做严谨的工程活，就用它。它自带二十三个 playbook：

```
/poteto-mode this pr has a subtle bug where the scroll drifts every 750ms even when idle. repro
first, then fix and verify.
```

```
/poteto-mode i'm going to bed. land the stack even if ci flakes. i want everything merged by
morning.
```

<details>
<summary>二十三个 playbook</summary>

| playbook | 用途 |
|---|---|
| [investigation](../skills/poteto-mode/playbooks/investigation.md) | 只读不改的提问：x 是怎么工作的？y 为什么这样建？我们确定吗？ |
| [bug fix](../skills/poteto-mode/playbooks/bug-fix.md) | 复现缺陷，找到根因，拿运行时证据把它修好。 |
| [perf](../skills/poteto-mode/playbooks/perf-issue.md) | 追查实测到的变慢，对照基线把它改快。 |
| [hillclimb](../skills/poteto-mode/playbooks/hillclimb.md) | 对照目标，持续、科学地改进一个指标。一轮轮验证假设，每轮都测改动前后的数值，每采纳一个改进就单独提交一次。 |
| [runtime forensics](../skills/poteto-mode/playbooks/runtime-forensics.md) | 借助插桩，诊断运行中的症状（泄漏、空闲时 CPU 空转、画面异常）。 |
| [trace forensics](../skills/poteto-mode/playbooks/trace-forensics.md) | 诊断一份已经采集好的性能剖析文件（cpuprofile、trace、spindump、heap snapshot）。 |
| [feature](../skills/poteto-mode/playbooks/feature.md) | 新增或修改行为，从一个命名好的数据形状建起。 |
| [refactoring](../skills/poteto-mode/playbooks/refactoring.md) | 改结构或形状，行为保持不变。 |
| [prototype](../skills/poteto-mode/playbooks/prototype.md) | 写个用完就扔的草稿，低成本地定下设计或行为。也可以跑起来观察，了结一个靠实验就能判断的分歧。 |
| [visual parity](../skills/poteto-mode/playbooks/visual-parity.md) | 让两种实现的界面逐像素一致。 |
| [authoring a skill](../skills/poteto-mode/playbooks/authoring-a-skill.md) | 写或改一份 SKILL.md。 |
| [eval](../skills/poteto-mode/playbooks/eval.md) | 盲测 skill 或提示词的改动会怎样影响 agent 的行为。 |
| [babysit](../skills/poteto-mode/playbooks/babysit.md) | 把一个 PR 或一整个 PR 栈推到可以合并：处理冲突、评审讨论和 CI。 |
| [shipping](../skills/poteto-mode/playbooks/shipping.md) | 独立验证一个全绿的 PR 栈，再从底部往上，把连续验证过的那一段落地。默认走 GitHub，能用 Origin 时走 Origin。 |
| [autonomous run](../skills/poteto-mode/playbooks/autonomous-run.md) | 中途不停，把一个长任务一直推到完成。 |
| [orchestrate](../skills/poteto-mode/playbooks/orchestrate.md) | 把一个长期项目交给一个负责协调的对话：要做好几天，有许多叠放的 PR，还有成群的子代理。 |
| [autopilot-full](../skills/poteto-mode/playbooks/autopilot-full.md) | 把互相独立的 PR 一路跑到合并。每个 PR 一个负责人。从代码就绪的 head 提交算起，每一轮都要等根 agent 用 swarm（一批并行的子代理）验证，给出 verdict（结论）。 |
| [autopilot-stack](../skills/poteto-mode/playbooks/autopilot-stack.md) | 搭好并验证一条线性的基分支栈，交给操作者审查并落地。 |
| [session pickup](../skills/poteto-mode/playbooks/session-pickup.md) | 接着做或接手之前某个 agent 没做完的工作。 |
| [pause safely](../skills/poteto-mode/playbooks/pause-safely.md) | 把做到一半的工作干净地停下来，方便以后接着做。 |
| [multi-phase plan](../skills/poteto-mode/playbooks/multi-phase-plan.md) | 跨多个阶段或多个叠放 PR 的工作。 |
| [worktree cleanup](../skills/poteto-mode/playbooks/worktree-cleanup.md) | 清掉已合并或已废弃的 worktree（Git 的附加工作目录）和过时的 iOS 模拟器，腾出磁盘空间。删之前有安全检查把关。 |
| [opening a pr](../skills/poteto-mode/playbooks/opening-a-pr.md) | 用一串小而有序的提交，开一个就绪（非草稿）的 PR。标题按 Conventional Commits 写，正文写成简报的样子。其他每个 playbook 结束时都会调用它。 |

</details>

调用后，它会：

1. 给你的任务匹配一个 [playbook](../skills/poteto-mode/SKILL.md)，再开一份待办清单。清单开头几项就是这个 playbook 的步骤，原封不动地抄进去。
2. 步骤触发时，转去调用其他 skill。
3. 写出 unslop（清掉 AI 腔）过的回复，写给使用者，也写给维护者。

完整的 rule 和 playbook 都在 [`skills/poteto-mode/SKILL.md`](../skills/poteto-mode/SKILL.md) 里。

[`/poteto-mode`](../skills/poteto-mode/SKILL.md) 还是一个会一直开着的 mode。进入以后，接下来每一轮对话它都在。有 playbook 对得上、或者任务需要严谨时，它自己起作用；其他时候它不碍事。想退出，随时说一声就行。

[`/poteto-mode`](../skills/poteto-mode/SKILL.md) 和 Cursor 的 `/loop` 命令配合得特别好。你可以让 Cursor 一连干好几个小时，严谨一点不打折扣。

## skills

下面这些 skill，大多会在步骤需要时由 [`/poteto-mode`](../skills/poteto-mode/SKILL.md) 替你运行（`how`、`why`、`architect`、`arena`、`swarm`、`interrogate`、`unslop`、`no-comments`、`technical-writing`、`tdd`，以及各条原则）。想直接用某一个时，就看这张表：

```
/how do we cancel runs? do we have an n+1 when we look up every run to cancel?
```

```
/interrogate review this pr.
```

<details>
<summary>全部 skill</summary>

| skill | 什么时候用 |
|---|---|
| [`/poteto-mode`](../skills/poteto-mode/SKILL.md) | 任何不简单的任务，默认都从这里开始。 |
| [`/how`](../skills/how/SKILL.md) | 你想要一份讲解，带你走一遍某个子系统是怎么工作的。 |
| [`/why`](../skills/why/SKILL.md) | 你想知道某样东西为什么这样建。它在运行时找出能用的 MCP，并行查询每一类证据（版本控制、问题跟踪、长篇文档、即时聊天、基础设施可观测性、错误跟踪、分析数据仓库）。 |
| [`/recall`](../skills/recall/SKILL.md) | 你正要开始或接着干活，想从自己的聊天记录和共享记录里，把某个主题最近的上下文重建起来，最后拿到一份精炼的现状简报。 |
| [`/blast-radius`](../skills/blast-radius/SKILL.md) | 你有一处看着很小的改动，想知道它还可能弄坏什么。它之所以安全，靠的是某一条事实；这条事实要跑代码来证明，不能只凭断言。 |
| [`/architect`](../skills/architect/SKILL.md) | 你马上要写跨函数边界的代码，想先把调用方的用法、类型和模块形状定下来。 |
| [`/arena`](../skills/arena/SKILL.md) | 你想让同一件事并行做 N 次，再从每一份里挑出最好的部分。 |
| [`/swarm`](../skills/swarm/SKILL.md) | 你想开 N 路并行，分头处理不同切片，或者同题竞赛，最后汇总成一份报告。 |
| [`/interrogate`](../skills/interrogate/SKILL.md) | 你有一份 diff，想让几个不同的模型想办法把它攻破，其中一个视角专门严查代码质量。 |
| [`/automate-me`](../skills/automate-me/SKILL.md) | 你想要一个自己的 `-mode` skill，照你实际的工作方式起草。 |
| [`/make-bot-ui`](../skills/make-bot-ui/SKILL.md) | 你想做一个页面或仪表盘，按上面的按钮就能经 webhook 唤醒一个 Grok Bot，连 sender key（发送方密钥）的交接和 Tailscale 也包括在内。 |
| [`/setup-pstack`](../skills/setup-pstack/SKILL.md) | 你想给 pstack 的每个角色挑模型。它会检测你有哪些模型，再写一条配置 rule。 |
| [`/reflect`](../skills/reflect/SKILL.md) | 一个长任务落地了，你想把这次的做法记下来，写成对 skill 的一处修改。 |
| [`/correct`](../skills/correct/SKILL.md) | 你在反复纠正代理犯同样的错。它从历史里找出错误类别，在够用的最高一层修掉每一类（先架构，再类型，再 lint 和 CI，然后测试，文档放最后），并留一张表，把每条规则和负责卡住它的机制配在一起。 |
| [`/teach`](../skills/teach/SKILL.md) | 你想真正弄懂一处改动或一个子系统，而不只是看个摘要。它跑 how + why，编成一份平实的讲解，一张图接一张图地搭起来。 |
| [`/tdd`](../skills/tdd/SKILL.md) | 你在修 bug，本地又能低成本地跑测试。先写会失败的测试，再写修复。 |
| [`/benchmark-checklist`](../skills/benchmark-checklist/SKILL.md) | 你跑了基准测试，或者测出了提速或退步。在你汇报这个数字或据此行动之前，它先替你核查一遍（限制因素、调优、错误、重复运行、对端到端是否重要）。 |
| [`/no-comments`](../skills/no-comments/SKILL.md) | 评审前去掉注释。它会启动 Comment Sicko，修好采纳的 finding（审查发现的问题）。注释里声称的约束，它会提议改由代码来保证。 |
| [`/typescript-best-practices`](../skills/typescript-best-practices/SKILL.md) | 你在读或改 TypeScript。它把 type-system-discipline 原则落到具体语法上。 |
| [`/figure-it-out`](../skills/figure-it-out/SKILL.md) | 自带的 playbook 都不合适。它为这个任务设计一份严谨、可审计的 playbook。 |
| [`/show-me-your-work`](../skills/show-me-your-work/SKILL.md) | 你想留一条能复查的决策 trail（逐条记下的决策记录）。它把决策写进一个可以提交的 tsv 文件。 |
| [`/create-verification-skill`](../skills/create-verification-skill/SKILL.md) | 你的项目还没有用脚本证明应用行为的办法。它生成一个只属于本项目的验证 skill，附带 feature map，什么语言、什么平台都行。 |
| [`/maintain-verification-skill`](../skills/maintain-verification-skill/SKILL.md) | 你的验证 skill 里的 feature map 已经跟应用对不上了。先一波并行读源码，再对运行中的应用走一轮，证实过的修正最多合成一个 PR。 |
| [`/unslop`](../skills/unslop/SKILL.md) | 你在清理文字。它去掉那些一看就是 AI 写的痕迹。 |
| [`/bro`](../skills/bro/SKILL.md) | 你想把上一条消息换成大白话重说一遍，不带行话。 |
| [`/technical-writing`](../skills/technical-writing/SKILL.md) | 分层的文档写作标准（Diátaxis + Google developer style + STE + Global English），用于文档、RFC、README、PR 描述和提交说明。 |

</details>

### 示例

我多半是在任务开头敲 [`/poteto-mode`](../skills/poteto-mode/SKILL.md)，让它自己转到某个 playbook。其他 skill 在步骤需要时触发。有几个我会直接拿来用。

<details>
<summary>全部示例</summary>

```
bug fix:           /poteto-mode this pr has a subtle bug where the scroll drifts every 750ms even
                   when idle. repro first, then fix and verify.
perf:              /poteto-mode a big list takes a second or two to load even though we virtualize.
                   run a cpu trace and tell me why.
feature:           /poteto-mode build a small feature behind a feature flag. verify it really works.
prototype:         /poteto-mode build two prototypes of the markdown renderer so we can compare.
                   spawn an agent for each.
multi-phase:       /poteto-mode open source these skills as a plugin. nothing internal leaks, work
                   in a temp dir, show me the dependency graph first.
overnight run:     /poteto-mode i'm going to bed. land the stack even if ci flakes. i want
                   everything merged by morning.
babysit:           /poteto-mode check on pr 123. anything outstanding?
visual parity:     /poteto-mode the row spacing is too tall when this flag is on. the second image
                   is correct. repro and fix until it matches.
figure it out:     /poteto-mode i'm stepping away. migrate every caller from the synchronous store
                   to the new async one, keeping behavior identical. i want to trust it was done
                   right when i'm back.
how:               /how do we cancel runs? do we have an n+1 when we look up every run to cancel?
why:               /why is this feature flag not on yet?
architect:         design this instrumentation to be high signal with no false positives. /architect
                   this first.
arena:             /arena take my prompt to the arena verbatim. i want to compare their proposals
                   with yours.
swarm:             /swarm check every package under packages/ against its check.sh. one worker per
                   package. one report.
interrogate:       /interrogate review this pr.
tdd:               /tdd implement
unslop:            can we unslop and tighten the new changes?
reflect:           /reflect that took too long. capture what we learned so the next run doesn't
                   repeat it.
correct:           /correct
show-me-your-work: /show-me-your-work keep a decision trail i can review when i'm back.
automate-me:       /automate-me
```

</details>

## `poteto-agent` 和 Comment Sicko 子代理

pstack 还带了一个子代理，从头到尾按我的风格干活。在父代理里用 [`subagent_type: "poteto-agent"`](../agents/poteto-agent.md) 启动它。动手之前，它会把 `poteto-mode` 完整读一遍，包括里面内嵌的原则索引。换成 `generalPurpose` 就会跳过这一步，越做越偏。

[`/poteto-mode`](../skills/poteto-mode/SKILL.md) 和 [`subagent_type: "poteto-agent"`](../agents/poteto-agent.md) 走的是同一层封装。

pstack 还带了 [Comment Sicko](../agents/comment-sicko.md)。它是一个只读的注释审查员，用 `subagent_type: "Comment Sicko"` 调用。一般经 [`/no-comments`](../skills/no-comments/SKILL.md) 来用，不直接调它。

## 原则

二十四个短小的 skill，每个讲一条原则。`poteto-mode` 在正文里给它们做了内嵌索引，任务开始时会读这份索引。之所以还要单独成文件，一是方便其他 skill 按名字引用某条原则，二是让索引能指向每条原则的完整 rule。

<details>
<summary>全部二十四条原则</summary>

| 原则 | 分组 | rule |
|---|---|---|
| [laziness-protocol](../skills/principle-laziness-protocol/SKILL.md) | 核心 | 偏向删除，偏向能解决问题的最小改动。 |
| [foundational-thinking](../skills/principle-foundational-thinking/SKILL.md) | 核心 | 写逻辑之前用：选核心类型和数据结构，安排先搭脚手架还是先做功能，问清并发的各方共享什么。数据结构选对了，后面的代码就一目了然。 |
| [redesign-from-first-principles](../skills/principle-redesign-from-first-principles/SKILL.md) | 核心 | 重新设计，就当这个需求从第一天起就是基础前提，而不是事后硬加上去。 |
| [attack-the-premise](../skills/principle-attack-the-premise/SKILL.md) | 核心 | 基于同一前提的两个或更多修复，都没过同一道关时用。下一次修复之前，先清点失衡落在哪些参与方身上，然后质疑这个前提，不要再写一个默认它成立的修复。 |
| [subtract-before-you-add](../skills/principle-subtract-before-you-add/SKILL.md) | 核心 | 先去掉累赘、多余的校验器和桩引用，再在更简单的基础上搭建。 |
| [minimize-reader-load](../skills/principle-minimize-reader-load/SKILL.md) | 核心 | 数一数从问题到答案隔了几层，读者脑子里还得记着多少隐藏状态。合并只有一个调用方的包装层，缩小可变状态的范围。 |
| [outcome-oriented-execution](../skills/principle-outcome-oriented-execution/SKILL.md) | 核心 | 用在阶段边界明确、事先规划好的重写和迁移里。直接收敛到目标架构，不要为了让中间状态平滑，写用完就扔的兼容代码。 |
| [experience-first](../skills/principle-experience-first/SKILL.md) | 核心 | 宁要用户用得开心，不要实现上图省事。宁可少交付几个打磨好的功能，也不多交付一堆粗糙的。 |
| [exhaust-the-design-space](../skills/principle-exhaust-the-design-space/SKILL.md) | 核心 | 拍板之前，先做 2-3 个互相竞争的原型，放在一起比。 |
| [build-the-lever](../skills/principle-build-the-lever/SKILL.md) | 核心 | 用于任何不简单的工作，不只是批量活：修改、迁移、分析、检查。别纯手工做，去做一个能完成它或证明它的工具（codemod、脚本、生成器，或者一份让子代理照着做的 skill）。这个工具就是审查者可以重跑的产物。 |
| [model-the-domain](../skills/principle-model-the-domain/SKILL.md) | 架构 | 把领域编进一个结构里，不要散成一堆条件判断。 |
| [boundary-discipline](../skills/principle-boundary-discipline/SKILL.md) | 架构 | 把防御检查集中在系统边界（CLI、配置、网络、外部 API）。信任内部类型，把业务逻辑放进纯函数。 |
| [type-system-discipline](../skills/principle-type-system-discipline/SKILL.md) | 架构 | 让非法状态无法表示。给有语义的原始类型打上品牌。在边界解析外部数据。不对编译器撒谎。穷举所有变体。从权威 schema 推导。 |
| [make-operations-idempotent](../skills/principle-make-operations-idempotent/SKILL.md) | 架构 | 不管之前的运行做到哪一步，最后都收敛到同一个终态。 |
| [migrate-callers-then-delete-legacy-apis](../skills/principle-migrate-callers-then-delete-legacy-apis/SKILL.md) | 架构 | 在同一波改动里迁走调用方、删掉旧 API，不留兼容层。 |
| [separate-before-serializing-shared-state](../skills/principle-separate-before-serializing-shared-state/SKILL.md) | 架构 | 先消除共享。只有当单一共享写入者确实是不变量时，才在结构上串行化。 |
| [prove-it-works](../skills/principle-prove-it-works/SKILL.md) | 验证 | 任务做完、宣布完成之前用。对着真实的产物验证（跑一遍功能、读实际的值、检查 diff），不拿替代指标、自我汇报或「能编译」充数。 |
| [fix-root-causes](../skills/principle-fix-root-causes/SKILL.md) | 验证 | 把每个症状追到根因，在根因处修。先复现，一路追问为什么，直到找到根因。忍住，别用空值检查把崩溃压下去。 |
| [sequence-verifiable-units](../skills/principle-sequence-verifiable-units/SKILL.md) | 验证 | 用于多步骤的工作（大范围清扫、迁移、一连串相似的修改），也用于安排提交和 PR 怎么叠。把工作拆成小单元，每个单元都停在可验证的状态，查完一个再做下一个。交付顺序也要排好，让这个序列自己向审查者证明它没问题。 |
| [test-behavior-not-implementation](../skills/principle-test-behavior-not-implementation/SKILL.md) | 验证 | 写测试、改测试，或决定留不留一个测试时用。像用户那样调用代码，拿他们看到的结果和一个字面量期望值做断言。如果每个导入的函数都返回 undefined，测试照样能过，那就重写断言，或者删掉这个测试。 |
| [explain-the-number](../skills/principle-explain-the-number/SKILL.md) | 验证 | 在相信、汇报一个自己测出的数字，或据此行动之前用：提速、退步、吞吐量、延迟，或者评测结果。找出是什么在限制它，并排除它测的其实是别的东西，而不是你以为的那项工作。 |
| [guard-the-context-window](../skills/principle-guard-the-context-window/SKILL.md) | 委派 | 大批量的活交给子代理。主线程只留摘要，不留原始数据。 |
| [never-block-on-the-human](../skills/principle-never-block-on-the-human/SKILL.md) | 委派 | 先做，再把结果拿给人看，让人事后纠偏。只有不可逆的操作才先确认。 |
| [encode-lessons-in-structure](../skills/principle-encode-lessons-in-structure/SKILL.md) | 元 | 把这条 rule 写成 lint、元数据标记、运行时检查或脚本，而不是再加一段文字。 |

</details>

## 没有打包进来的

有几样东西，`poteto-mode` 会引用，但没有一起打包：

- `/deslop` 和 `deslop` skill 在 `cursor-team-kit` 插件里。
- `control-cli`（用于 CLI 和 TUI）和 `control-ui`（用于浏览器、Electron、网页）也在 `cursor-team-kit` 里。
- `/create-skill` 是 Cursor 内置的。Cursor 也内置了一个 `/babysit`。在 `poteto-mode` 里，问 PR 状态的请求由 [babysit playbook](../skills/poteto-mode/playbooks/babysit.md) 接手，不用内置的那个。

想要全套的话，把 `cursor-team-kit` 和 pstack 一起装上。

## 为什么不做规划 skill

Cursor 已经有一个很好用的 plan mode，和 pstack 配合得也很好。但我个人不信规划这一套。最好的规格说明就是代码。你要是确实想先做计划，[`/poteto-mode`](../skills/poteto-mode/SKILL.md) 也能做，只是不默认这么做。

## 把它变成你的

`poteto-mode` 是我的风格，你未必想要一模一样的。

输入 [`/automate-me`](../skills/automate-me/SKILL.md)。它会翻你最近的对话记录，照你实际的工作方式起草一个 `<your-name>-mode` skill，底层仍然走 pstack。pstack 照旧当底座，你在 `poteto-mode` 之外，多了一个自己的路由 skill。

模型也能配。输入 [`/setup-pstack`](../skills/setup-pstack/SKILL.md)。它会检测你能用哪些模型，写一条小小的 always-applied rule（始终生效的 rule），给每个角色（写代码、判断、各个评审团）指定一个模型。每个 skill 都会读这条 rule；rule 不在时，就退回合理的默认值。所以你只需要改想改的那几项。

0.15.3 之前写的 rule，把模型锁在了旧的默认值上。删掉那几行角色设置，或者干脆删掉整个文件，再跑一次 `/setup-pstack`。重跑时，模型和默认值不同的角色都会保留。

## 自动化

pstack 还带了一个默认不启用的 [Benny 自动化包](https://github.com/cursor/plugins/tree/12d587dfb20741cafc376c42c696c5f6e2a64487/pstack/automations/benny)。Benny 会筛查 Slack 上报来的问题，确认是 bug 的，再拿真实的界面证据复现并修好。它的文件没有注册成斜杠 skill。

要配置它，让 Cursor 照着 [`FOR_AGENTS.md`](https://github.com/cursor/plugins/blob/12d587dfb20741cafc376c42c696c5f6e2a64487/pstack/automations/benny/FOR_AGENTS.md) 做。配置时会把这个包复制到目标仓库的 `.cursor/automations/benny/`，在那个仓库里启用 pstack 好共用 skill，并把用户配置放在复制过去的包外面。

## 许可证

MIT
