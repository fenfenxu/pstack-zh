---
title: "pstack 插件说明"
description: "pstack 是 poteto 每天在 Cursor 用来交付高质量代码的同一套 skill。它帮你写得更少，质量更高。"
sourceUrl: "https://github.com/cursor/plugins/blob/12d587dfb20741cafc376c42c696c5f6e2a64487/pstack/README.md"
---

> [!NOTE]
> **来源**
>
> 这是中文译文，方便对照阅读。本站不发行中文版插件。英文原文 [`pstack/README.md`](https://github.com/cursor/plugins/blob/12d587dfb20741cafc376c42c696c5f6e2a64487/pstack/README.md)，取自 pstack 官方仓库 cursor/plugins 的提交 `12d587d`。
>
> [本站英文页](https://pstack.droplink.cloud/en/skills-zh/official-guide/plugin-readme/)

# pstack

我是 [poteto](https://x.com/poteto)。我不是总裁，也不是 CEO。我在 Meta、Netflix 和 Cursor 碰过数百万行代码。我也在 react 核心团队，帮忙构建和维护 react compiler。

越来越多人觉得 AI 写出太多 slop 代码。我同意。我不想像一支二十人的 slop 艺人队伍那样交付。没有质量的吞吐，不是我追求的目标。想走得快，就先走得深。

**pstack 是我的答案。** 这就是我每天在 Cursor 用来交付高质量代码的同一套 skill。它把 Cursor 变成一支真正的工程团队。目标不是把 loc 拉到最大。方向正好相反。pstack 帮你写得更少，但代码质量更高。

**pstack 给你无畏的并行。** 当你能在一个代理上走深，并信任它写出好的、可验证的代码，你才能真正有信心地并行。用 `poteto-mode` 拉起多个代理，并信任它们会把严格的工程原则用到工作上。

**Cursor 把各家的长处都给你。** 每个 frontier 模型都有自己的强弱。pstack 配任何模型都能用。我的许多 skill 用多模型工作流，来用上每个模型独有的长处。

fork 它。改进它。把它变成你的。欢迎 PR。

## 安装

```bash
/add-plugin pstack
```

## 开始

两步：

1. 运行 [`/setup-pstack`](../skills/setup-pstack/SKILL.md)，选一个推理预算，并选好你要的模型。
2. 做任何需要严格的事时，用 [`/poteto-mode`](../skills/poteto-mode/SKILL.md)。

刚来？[pstack 指南](./index.md) 带你走完第一个真实任务。从配置和提示词，到验证和过夜运行。

就这些。其他 skill 看情况用。mode skill 会在需要时替你用它们。开箱时，这个 mode 按模型的长处拆工作：代码委派（feature、refactoring、bug fix、perf、hillclimb）走 grok。最难的改动、正文和判断走 opus 5.5。默认评审团是 opus 5.5 / sol / grok。[`/setup-pstack`](../skills/setup-pstack/SKILL.md) 可以改其中任何一项。

## 用法

在任务开始时用 [`/poteto-mode`](../skills/poteto-mode/SKILL.md)。它读你的请求，从一套 playbook 里挑选，并在步骤需要时运行其他 skill。

### 就用 [`/poteto-mode`](../skills/poteto-mode/SKILL.md)

这个 skill 是主要的快捷方式。每当我需要代理做严格的工程工作时，我就用它。它带二十三个 playbook：

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
| [investigation](../skills/poteto-mode/playbooks/investigation.md) | 一个只读问题。x 如何工作，为什么 y 要这样建，我们确定吗。 |
| [bug fix](../skills/poteto-mode/playbooks/bug-fix.md) | 复现一个缺陷，找出根因，并用运行时证据修复。 |
| [perf](../skills/poteto-mode/playbooks/perf-issue.md) | 追溯一次测到的变慢，并相对基线改进它。 |
| [hillclimb](../skills/poteto-mode/playbooks/hillclimb.md) | 针对一个指标、对着一个目标，做持续的科学改进。假设一轮轮循环，每次都有前后测量，每个被接受的胜利一次提交。 |
| [runtime forensics](../skills/poteto-mode/playbooks/runtime-forensics.md) | 从插桩诊断一个现场症状（泄漏、空闲 CPU 空转、毛刺）。 |
| [trace forensics](../skills/poteto-mode/playbooks/trace-forensics.md) | 诊断一份已采集的剖析产物（cpuprofile、trace、spindump、heap snapshot）。 |
| [feature](../skills/poteto-mode/playbooks/feature.md) | 新的或改过的行为，从一个命名的数据形状建出来。 |
| [refactoring](../skills/poteto-mode/playbooks/refactoring.md) | 保持行为不变的结构或形状改动。 |
| [prototype](../skills/poteto-mode/playbooks/prototype.md) | 一份用完即弃的草图，用来便宜地做设计或行为决定，或靠观察了结一个经验上的分叉。 |
| [visual parity](../skills/poteto-mode/playbooks/visual-parity.md) | 两种实现之间像素级的 UI 等价。 |
| [authoring a skill](../skills/poteto-mode/playbooks/authoring-a-skill.md) | 编写或编辑一份 SKILL.md。 |
| [eval](../skills/poteto-mode/playbooks/eval.md) | 测试 skill 或提示词改动如何影响代理行为。盲测。 |
| [babysit](../skills/poteto-mode/playbooks/babysit.md) | 把一个 PR 或一个栈推到可合并：冲突、审查线程、CI。 |
| [shipping](../skills/poteto-mode/playbooks/shipping.md) | 独立验证一个绿色栈，然后把连续已验证的那一段从底部往上落地。默认经 GitHub。Origin 可用时走 Origin。 |
| [autonomous run](../skills/poteto-mode/playbooks/autonomous-run.md) | 把一个长任务赶到完成，中途不停。 |
| [orchestrate](../skills/poteto-mode/playbooks/orchestrate.md) | 交给一个协调对话的常驻项目：多日、许多叠放的 PR、一队子代理。 |
| [autopilot-full](../skills/poteto-mode/playbooks/autopilot-full.md) | 把独立 PR 跑到已合并。每个 PR 一个负责者。从代码就绪的 head 起，每一轮有一个根 swarm 的 verdict。 |
| [autopilot-stack](../skills/poteto-mode/playbooks/autopilot-stack.md) | 构建并验证一条线性的基分支栈，交给操作者复审和落地。 |
| [session pickup](../skills/poteto-mode/playbooks/session-pickup.md) | 恢复或接管先前代理进行中的工作。 |
| [pause safely](../skills/poteto-mode/playbooks/pause-safely.md) | 干净地暂停进行中的工作，以便以后恢复。 |
| [multi-phase plan](../skills/poteto-mode/playbooks/multi-phase-plan.md) | 跨阶段或叠放 PR 的工作。 |
| [worktree cleanup](../skills/poteto-mode/playbooks/worktree-cleanup.md) | 修剪已合并或已放弃的 worktree，以及过期的 iOS 模拟器，收回磁盘。有安全门。 |
| [opening a pr](../skills/poteto-mode/playbooks/opening-a-pr.md) | 从小而有序的提交开一个就绪的 PR。标题用 conventional commits，正文用简报体。每个其他 playbook 结束时都会调用它。 |

</details>

被调用时，它会：

1. 把你的任务匹配到一个 [playbook](../skills/poteto-mode/SKILL.md)，并打开一份待办列表。最前面几项就是它的步骤，逐字抄入。
2. 步骤触发时，路由到其他 skill。
3. 写出 unslop 过的回复，同时写给使用者和维护者。

完整的 rule 和 playbook 在 [`skills/poteto-mode/SKILL.md`](../skills/poteto-mode/SKILL.md)。

[`/poteto-mode`](../skills/poteto-mode/SKILL.md) 也是一个粘性 mode。一旦进入，它会跨回合保持开启。playbook 匹配，或任务需要严格时，它自己应用。其他时候它让开。随时可以说一声来退出。

[`/poteto-mode`](../skills/poteto-mode/SKILL.md) 和 Cursor 的 `/loop` 命令配合得非常好。你可以让 Cursor 工作很多小时，同时不牺牲严格。

## skills

步骤需要时，[`/poteto-mode`](../skills/poteto-mode/SKILL.md) 会替你运行其中大部分（`how`、`why`、`architect`、`arena`、`swarm`、`interrogate`、`unslop`、`no-comments`、`technical-writing`、`tdd`，以及各条原则）。下面的表是给你想直接用某一个的时候：

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
| [`/poteto-mode`](../skills/poteto-mode/SKILL.md) | 任何不算小的任务的默认入口。 |
| [`/how`](../skills/how/SKILL.md) | 你想要一份子系统如何工作的走读。 |
| [`/why`](../skills/why/SKILL.md) | 你想知道为什么要这样建。它在运行时发现可用的 MCP，并并行查询每一类证据（源码控制、问题跟踪、长文文档、实时聊天、基础设施可观测性、错误跟踪、分析数据仓库）。 |
| [`/recall`](../skills/recall/SKILL.md) | 你在开始或恢复工作，想从你自己的聊天历史和共享记录里重建最近的上下文，交回一份紧凑的当前状态简报。 |
| [`/blast-radius`](../skills/blast-radius/SKILL.md) | 你有一处看起来很小的改动，想知道它还可能弄坏什么。它之所以安全所靠的那一个事实，用跑代码证明，不用断言。 |
| [`/architect`](../skills/architect/SKILL.md) | 你正要写越过函数边界的代码，想先把调用方的用法、类型和模块形状定下来。 |
| [`/arena`](../skills/arena/SKILL.md) | 你想对同一件事做 N 次并行尝试，然后抓取每一份里最好的部分。 |
| [`/swarm`](../skills/swarm/SKILL.md) | 你想要 N 个并行 worker，跨不同切片或竞赛，然后要一份汇总报告。 |
| [`/interrogate`](../skills/interrogate/SKILL.md) | 你有一份 diff，想让几个不同的模型试着打破它，包括一个严格的代码质量视角。 |
| [`/automate-me`](../skills/automate-me/SKILL.md) | 你想要自己的 `-mode` skill，按你实际工作的方式起草。 |
| [`/make-bot-ui`](../skills/make-bot-ui/SKILL.md) | 你想要一个页面或仪表盘，按钮经 webhook 唤醒一个 Grok Bot，包括发送方密钥交接和 Tailscale。 |
| [`/setup-pstack`](../skills/setup-pstack/SKILL.md) | 你想按角色挑选 pstack 用哪些模型。它检测你的模型，并写出一条配置 rule。 |
| [`/reflect`](../skills/reflect/SKILL.md) | 一个长任务落地了，你想把配方留成一次 skill 编辑。 |
| [`/teach`](../skills/teach/SKILL.md) | 你想真正理解一处改动或一个子系统，而不只是拿到摘要。它跑 how + why，并织成一份白话说明，一张图一张图往上建。 |
| [`/tdd`](../skills/tdd/SKILL.md) | 你在修一个 bug，并且有一条便宜的本地测试路径。先写失败测试，再写修复。 |
| [`/no-comments`](../skills/no-comments/SKILL.md) | 复审前去掉注释。它启动 Comment Sicko，修复被接受的 finding，并为声称的约束提供编码。 |
| [`/typescript-best-practices`](../skills/typescript-best-practices/SKILL.md) | 你在读或改 TypeScript。把 type-system-discipline 原则落到语法上。 |
| [`/figure-it-out`](../skills/figure-it-out/SKILL.md) | 没有打包的 playbook 合适。为这个任务设计一份严格、可审计的 playbook。 |
| [`/show-me-your-work`](../skills/show-me-your-work/SKILL.md) | 你想要一条可复审的决策 trail。把决定记到一份你可以提交的 tsv。 |
| [`/create-verification-skill`](../skills/create-verification-skill/SKILL.md) | 你的项目没有脚本化的办法来证明应用行为。生成一个项目本地的验证 skill，带一份 feature map。任何语言或平台都可以。 |
| [`/maintain-verification-skill`](../skills/maintain-verification-skill/SKILL.md) | 你的验证 skill 的 feature map 已经和应用漂开。源码一波，加一轮现场通过。已证明的修正最多一个 PR。 |
| [`/unslop`](../skills/unslop/SKILL.md) | 你在清理文字。去掉 AI 的口吻痕迹。 |
| [`/bro`](../skills/bro/SKILL.md) | 你想把上一条消息用白话重说，没有行话。 |
| [`/technical-writing`](../skills/technical-writing/SKILL.md) | 分层的文档标准（Diátaxis + Google developer style + STE + Global English），用于文档、RFC、readme、PR 描述、提交说明。 |

</details>

### 示例

大多数时候，我在任务开始时打 [`/poteto-mode`](../skills/poteto-mode/SKILL.md)，让它路由到一个 playbook。其他 skill 在步骤需要时触发。有几个我会直接伸手去用。

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
show-me-your-work: /show-me-your-work keep a decision trail i can review when i'm back.
automate-me:       /automate-me
```

</details>

## `poteto-agent` 和 Comment Sicko 子代理

pstack 还带一个子代理，端到端跑我的风格。从父代理通过 [`subagent_type: "poteto-agent"`](../agents/poteto-agent.md) 启动它。它在做任何工作之前，会完整读 `poteto-mode`，包括内联的原则索引。换成 `generalPurpose` 会跳过这次阅读，然后跑偏。

[`/poteto-mode`](../skills/poteto-mode/SKILL.md) 和 [`subagent_type: "poteto-agent"`](../agents/poteto-agent.md) 走同一个包装。

pstack 还带 [Comment Sicko](../agents/comment-sicko.md)，一个只读的注释审查者，可用 `subagent_type: "Comment Sicko"`。通常通过 [`/no-comments`](../skills/no-comments/SKILL.md) 调用，不直接调。

## 原则

二十三个短 skill，每个一条原则。`poteto-mode` 把它们内联索引，并在任务开始时读这份索引。独立文件在那里，是为了让其他 skill 能按名字引用一条原则，也是为了让索引能指向每一条的完整 rule。

<details>
<summary>全部二十三条原则</summary>

| 原则 | 分组 | rule |
|---|---|---|
| [laziness-protocol](../skills/principle-laziness-protocol/SKILL.md) | 核心 | 偏向删除，以及能解决问题的最小改动。 |
| [foundational-thinking](../skills/principle-foundational-thinking/SKILL.md) | 核心 | 写逻辑之前先用：选定核心类型和数据结构，排列脚手架与功能的先后，问并发行动者共享什么。把数据结构弄对，下游代码就会变得明显。 |
| [redesign-from-first-principles](../skills/principle-redesign-from-first-principles/SKILL.md) | 核心 | 把需求当成从第一天就有的基础假设来重新设计，不要栓上去。 |
| [attack-the-premise](../skills/principle-attack-the-premise/SKILL.md) | 核心 | 当两个或更多共享同一前提的修复都没通过同一道门时使用。下一次修复之前，先普查哪些行动者持有不平衡，然后质疑前提。不要再写一个假设该前提成立的修复。 |
| [subtract-before-you-add](../skills/principle-subtract-before-you-add/SKILL.md) | 核心 | 先去掉死重、多余的校验器和桩引用，再在更简单的底上建造。 |
| [minimize-reader-load](../skills/principle-minimize-reader-load/SKILL.md) | 核心 | 数一数问题和答案之间有几层，以及读者脑子里的隐藏状态。折叠只有一个调用方的包装，并缩小可变范围。 |
| [outcome-oriented-execution](../skills/principle-outcome-oriented-execution/SKILL.md) | 核心 | 用于有明确阶段边界的计划中重写和迁移。收敛到目标架构。不要用用完即弃的兼容代码来保住平滑的中间状态。 |
| [experience-first](../skills/principle-experience-first/SKILL.md) | 核心 | 选择用户的愉悦，不选实现上的方便。少交付打磨过的功能，不多交付粗糙的功能。 |
| [exhaust-the-design-space](../skills/principle-exhaust-the-design-space/SKILL.md) | 核心 | 承诺之前，做 2-3 个互相竞争的原型，并排比较。 |
| [build-the-lever](../skills/principle-build-the-lever/SKILL.md) | 核心 | 用于任何不算小的工作，不只是批量工作：编辑、迁移、分析、检查。做出能做这件事或证明这件事的工具（codemod、脚本、生成器，或一份子代理遵循的 skill），不要纯手工做。这个工具就是审查者可以重跑的产物。 |
| [model-the-domain](../skills/principle-model-the-domain/SKILL.md) | 架构 | 把领域编进一个结构，不要散落的条件判断。 |
| [boundary-discipline](../skills/principle-boundary-discipline/SKILL.md) | 架构 | 把守卫集中在系统边界（CLI、配置、网络、外部 API）。信任内部类型，并把业务逻辑放在纯函数里。 |
| [type-system-discipline](../skills/principle-type-system-discipline/SKILL.md) | 架构 | 让非法状态无法被表示。给语义原语打上品牌。在边界解析外部数据。拒绝骗编译器。穷尽变体。从权威 schema 推导。 |
| [make-operations-idempotent](../skills/principle-make-operations-idempotent/SKILL.md) | 架构 | 无论之前的运行完成了多少，都收敛到同一个终态。 |
| [migrate-callers-then-delete-legacy-apis](../skills/principle-migrate-callers-then-delete-legacy-apis/SKILL.md) | 架构 | 在同一波里迁移调用方并删除旧 API，不要保留兼容层。 |
| [separate-before-serializing-shared-state](../skills/principle-separate-before-serializing-shared-state/SKILL.md) | 架构 | 先消除共享。只有当单一共享写入者是真正的不变量时，才在结构上串行化。 |
| [prove-it-works](../skills/principle-prove-it-works/SKILL.md) | 验证 | 完成任务之后、宣布做完之前使用。对照真实产物验证（跑这个功能、读实际的值、检查 diff）。不要对照替身、自我报告，或「能编译」。 |
| [fix-root-causes](../skills/principle-fix-root-causes/SKILL.md) | 验证 | 把每个症状追溯到根因，并在那里修复。先复现。一直问为什么，直到到达根因。抵制用空值检查守卫把崩溃消音。 |
| [sequence-verifiable-units](../skills/principle-sequence-verifiable-units/SKILL.md) | 验证 | 用于多步工作（扫荡、迁移、一串相似编辑），也用于你如何叠放提交和 PR。把工作拆成小单元，每个都以可验证状态结束。先检查一个，再做下一个。把交付排好序，让序列自己向审查者证明。 |
| [test-behavior-not-implementation](../skills/principle-test-behavior-not-implementation/SKILL.md) | 验证 | 在你编写、修改或保留一个测试时使用。按用户的方式调用代码，并把他们观察到的结果对一个字面期望值做断言。如果每个导入的函数都返回 undefined，测试仍会通过，就改写断言，或删掉测试。 |
| [guard-the-context-window](../skills/principle-guard-the-context-window/SKILL.md) | 委派 | 把大批量路由给子代理。主线程里留摘要，不留原始载荷。 |
| [never-block-on-the-human](../skills/principle-never-block-on-the-human/SKILL.md) | 委派 | 继续做，交出结果，让人事后纠正方向。确认只留给不可逆的动作。 |
| [encode-lessons-in-structure](../skills/principle-encode-lessons-in-structure/SKILL.md) | 元 | 把这条 rule 编成 lint、元数据标记、运行时检查或脚本，不要再加文字。 |

</details>

## 这份说明没写进去的

有几样东西 `poteto-mode` 会引用，但没有打包进来：

- `/deslop` 和 `deslop` skill 在 `cursor-team-kit` 插件里。
- `control-cli`（用于 CLI 和 TUI）和 `control-ui`（用于浏览器、Electron、web）也在 `cursor-team-kit` 里。
- `/create-skill` 是 Cursor 内置的。Cursor 也带一个内置的 `/babysit`。在 `poteto-mode` 里面，[babysit playbook](../skills/poteto-mode/playbooks/babysit.md) 在 PR 状态请求上取代它。

如果你要全套，把 `cursor-team-kit` 和 pstack 一起装上。

## 为什么不做规划 skill

Cursor 已经有一个很好的 plan mode，和 pstack 配合得很好。但我自己不相信规划。最好的规格就是代码。如果你确实想做计划，[`/poteto-mode`](../skills/poteto-mode/SKILL.md) 能覆盖，只是它不是默认。

## 把它变成你的

`poteto-mode` 是我的风格。你也许不想要完全一样的。

输入 [`/automate-me`](../skills/automate-me/SKILL.md)。它挖掘你最近的对话记录，按你实际工作的方式起草一个 `<your-name>-mode` skill，并在底下路由到 pstack。你保留 pstack 作为底座，并在 `poteto-mode` 旁边得到你自己的路由 skill。

模型也可以配置。输入 [`/setup-pstack`](../skills/setup-pstack/SKILL.md)。它检测你能用的模型，并写出一条小的 always-applied rule，把每个角色（代码、判断、评审团）映射到一个模型。每个 skill 都会读它。rule 不在时，回落到合理的默认。所以你只覆盖你想覆盖的。

0.15.3 之前写的 rule 钉住的是旧的默认模型。删掉那些角色行，或者删掉文件，然后重新运行 `/setup-pstack`。再跑一次时，模型与默认不同的角色会保留。

## 自动化

pstack 还带一份休眠的 [Benny 自动化包](https://github.com/cursor/plugins/tree/12d587dfb20741cafc376c42c696c5f6e2a64487/pstack/automations/benny)。Benny 分拣 Slack 的问题报告，然后用真实的 UI 证据复现并修复已确认的 bug。它的文件没有注册成斜杠 skill。

要配置它，把 Cursor 指向 [`FOR_AGENTS.md`](https://github.com/cursor/plugins/blob/12d587dfb20741cafc376c42c696c5f6e2a64487/pstack/automations/benny/FOR_AGENTS.md)。配置会把这份包复制到目标仓库的 `.cursor/automations/benny/`，在那里启用 pstack 以共享 skill，并把用户配置留在复制的包外面。

## 许可证

MIT
