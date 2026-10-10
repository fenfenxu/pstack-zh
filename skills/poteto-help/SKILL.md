---
name: poteto-help
description: 带你完成 pstack 安装、/poteto-mode，以及为任务挑选 skill、playbook 或原则。输入 /poteto-help 加上问题即可。
disable-model-invocation: true
---

:::note[导读]
用 `/poteto-help` 问 pstack 怎么用。这页把问题转到对应的 skill、playbook 或指南，并给一段可以发出去的提示词。问的是用法，不要直接开工。
:::

# Poteto help

回答对方关于 pstack 的问题，给一段可以发出去的提示词，并链到答案所在的文件。这是在问用法，不要开工。对方问的是怎么做。跑一次 pstack 会花掉实打实的 token，所以让对方自己把提示词发出去。

如果对方是在派活，比如「use pstack to fix this bug」，那就不是在问用法。先读 [`poteto-mode`](../poteto-mode/SKILL.md)，按它干活，并提一次：用 Custom Mode（一直开着的 mode，后续每一轮都在）能让它保持生效。

本文件把问题转到真正写着答案的 skill 和指南页。细节以那些文件为准。引用之前先读你转到的那一页。它和这张对照表不一致时，信那一页。这里的链接指向已安装的插件，对方未必打得开。把该文件的公开副本给对方：`https://github.com/cursor/plugins/blob/main/pstack/` 再加上它的路径。

## 弄清对方要什么

从这条消息和整段对话推断对方要什么。已经点名场合的，比如「which skill reviews a PR?」，直接进对应那一节。还看不清，就问一道选择题，选项如下，然后只答对方选的那一节：

- 完成安装
- 用 `/poteto-mode` 开工
- 按场合挑一个 skill
- 修好跑偏的一次运行
- 把 pstack 变成我的

先核对会改答案的状态，只有真的改了答案时才提：

- 没有 `~/.cursor/rules/pstack-models.mdc`，说明这个人还没跑过 `/setup-pstack`，每个角色都在用默认模型。
- 项目里没有 `verify-*` skill，也没有别的应用 harness（能驱动应用、检查结果的测试工具），agent 就没有脚本化的办法去操作这个应用。问题如果是在问怎样证明改动有效，就提一下 `/create-verification-skill`。

模型 rule 不在，而且这件事要紧，就问对方要不要现在给每个角色挑模型、再定一个推理预算。对方是新人、问的是安装或花费，或者答案取决于跑哪些模型，这时就要紧。一次聊天最多问一次。需要也不清时，两个问题一起问。给两个选项：

- 现在就配：把 `/setup-pstack` 交给对方去输入，同时答他们的问题。
- 以后再配：先答问题，并加一句：在跑 `/setup-pstack` 之前，每个角色都继续用默认模型。

## 完成安装

1. 在聊天里用 `/add-plugin pstack` 安装，或从侧栏的 Customize 安装。
2. 跑 [`/setup-pstack`](../setup-pstack/SKILL.md)。它会问推理预算，给每个角色配一个模型，并写出一条 rule。这条 rule 从新聊天开始生效。
3. 用 `/poteto-mode` 开工，带上一个目标和一个能判定通过或失败的检查。

装上以后，对方还没调用 skill 之前，什么都不会变。只有 `/setup-pstack` 会从对方的话里自行加载。细节见 [插件说明](../../guide/plugin-readme.md) 和 [安装 pstack](../../guide/01-setup.md)。可以按 [`references/prompting.md`](./references/prompting.md) 一起写第一条提示词。

对方担心花费，就说清 token 花在哪，以及怎样少花一点。pstack 会在子代理和评审团上多花 token。再跑一次 `/setup-pstack`，选更小的预算或更便宜的模型。角色设成 `auto` 或 `inherit-parent` 时，跟着当前聊天的模型跑。聊天本身用 Auto 或更便宜的模型，就能省 token。评审团名单更短，子代理就更少，名单有几项就跑几个。`/poteto-mode` 留给真正需要严谨的工作。

pstack 是为 Cursor 做的。它的 skill 用的是 Agent Skills 格式，别的工具也能读。但大多数工作流 skill，包括 `/poteto-mode`、`/how`、`/why` 和 `/teach`，都会按角色模型拉起 Cursor 子代理；Custom Mode 和 `/loop` 也是 Cursor 的功能。换到别的工具上，这些部分可能用不了。

## 用 `/poteto-mode` 开工

`/poteto-mode` 给任务匹配一个 playbook（针对某类任务写好的一套步骤），把步骤抄进 todo 列表，再按步骤需要跑其他 skill。跳过的步骤仍留在列表里，标着 `skip: <reason>`。好的提示词写清目标和怎样算做完。不要把 skill 一个个列出来。手写的顺序往往会漏掉或打乱 playbook 本来会保留的步骤。帮对方写之前，先读 [`references/prompting.md`](./references/prompting.md)。例子见 [把工作交给 `/poteto-mode`](../../guide/02-poteto-mode.md)。

`/poteto-mode` 会不会一直开着，取决于怎么启动：

- 在 `/poteto-mode` 上按 Enter，这个 skill 只挂在这一条消息上。对话往前走，它就会淡掉。
- 在 Mac 上按 Option+Enter，在 Windows 上按 Alt+Enter，或在 skill 条目里选 Use as Mode，会把它做成 Custom Mode。在对方退出这个 mode 之前，每一轮它都在上下文里，闲聊的轮次它不插手。
- Cursor 的文档在 Agents Window 和 CLI 里列了 Custom Mode。在别处，每个新任务都用 `/poteto-mode` 开头。

说到这里，链到 [Cursor 的 skills 文档](https://cursor.com/docs/skills)。聊到一半时，说「new task」会让这个 mode 重新匹配一个 playbook。`/poteto-mode` 给 playbook 步骤拉起的子代理，已经在用 `poteto-agent`。自己拉子代理也想要同样的风格，就用 `subagent_type: "poteto-agent"`。

## 挑一个 skill

默认答案是 `/poteto-mode`，步骤需要时，它会跑大多数其他 skill。对方想要比 playbook 更多或更少的某样东西时，再直接点名一个 skill。推荐之前先读那个 skill，并给一条示例提示词。

| 读者想做的事 | skill |
|---|---|
| 任何不简单的任务，都要严谨地做 | [`/poteto-mode`](../poteto-mode/SKILL.md) |
| 弄清代码现在怎么运作，或新代码该放哪 | [`/how`](../how/SKILL.md) |
| 弄清代码为什么是这个形状，或某个数字从哪来 | [`/why`](../why/SKILL.md) |
| 用大白话弄懂一次改动或一个子系统 | [`/teach`](../teach/SKILL.md) |
| 弄清自己最近在某个话题上的工作 | [`/recall`](../recall/SKILL.md) |
| 弄清一小段 diff 可能在自身以外弄坏什么 | [`/blast-radius`](../blast-radius/SKILL.md) |
| 跨函数边界的代码动手前，先定下类型和模块形状 | [`/architect`](../architect/SKILL.md) |
| 同一份简报多试几次，再合成最好的那一份 | [`/arena`](../arena/SKILL.md) |
| 按切片并行检查，或让 worker（并行干活的子代理）竞速，并以 cloud agent 来跑 | [`/swarm`](../swarm/SKILL.md) |
| 让不同模型审一份 diff，并试着把它挑破 | [`/interrogate`](../interrogate/SKILL.md) |
| 本地有便宜测试时，先写测试再修 bug | [`/tdd`](../tdd/SKILL.md) |
| 把 TypeScript 规则用到 `.ts` 或 `.tsx` 工作上 | [`/typescript-best-practices`](../typescript-best-practices/SKILL.md) |
| 审查前清掉注释，换一个没写过这些注释的审查者 | [`/no-comments`](../no-comments/SKILL.md) |
| 清掉文字里的 AI 痕迹 | [`/unslop`](../unslop/SKILL.md) |
| 按标准写文档、RFC、README、PR 描述或 commit 信息 | [`/technical-writing`](../technical-writing/SKILL.md) |
| 用大白话再听一遍上一条回复 | [`/bro`](../bro/SKILL.md) |
| 给 agent 一套脚本化的办法去操作应用、证明行为 | [`/create-verification-skill`](../create-verification-skill/SKILL.md) |
| 让验证 skill 和它的 feature map（对照应用功能写的验证地图）重新跟应用对齐 | [`/maintain-verification-skill`](../maintain-verification-skill/SKILL.md) |
| 汇报或据此行动之前，先核一遍性能数字 | [`/benchmark-checklist`](../benchmark-checklist/SKILL.md) |
| 跑一次大改或横切改动，或离开一阵再回来审的工作 | [`/figure-it-out`](../figure-it-out/SKILL.md) |
| 运行期间留决策日志，事后再审 | [`/show-me-your-work`](../show-me-your-work/SKILL.md) |
| 给每个角色挑模型，再定一个推理预算 | [`/setup-pstack`](../setup-pstack/SKILL.md) |
| 把自己的工作习惯做成一份个人 mode skill | [`/automate-me`](../automate-me/SKILL.md) |
| 把做完的任务里学到的东西写成 skill 改动 | [`/reflect`](../reflect/SKILL.md) |
| 别让 agent 在这个仓库里重复同样的错 | [`/correct`](../correct/SKILL.md) |
| 做一页带按钮的页面，用 webhook 唤醒 Grok Bot | [`/make-bot-ui`](../make-bot-ui/SKILL.md) |
| 在 pstack 里找路 | `/poteto-help` |

旁边某个 skill 目录没出现在这张表里，就读它的 front matter，按 `description` 来转。`principle-*` 那些目录，见下面的原则一节。

不好分的时候：

- `/how` 讲代码现在做什么。`/why` 讲原因。`/teach` 会跑其中之一或两个都跑，再用大白话讲结果。
- `/arena` 给每个 worker 同一份简报，再合成最好的部分。`/swarm` 把工作拆成切片或一场竞速，交回一份报告。
- `/architect` 定下设计后马上实现。加上「with checkpoint」，就能在它写代码之前先审设计。
- `/interrogate` 审这份 diff。`/blast-radius` 找 diff 以外会坏的地方，并证明那一件能让这次改动安全的事实。
- `/recall` 从最近的聊天里重建上下文。接着某一个具体的聊天或分支往下做，用的是 Session pickup playbook。
- `/figure-it-out` 设计一次严谨的运行。Orchestrate playbook 跑一个跨好几天、许多 PR 的项目。Autonomous run playbook 把一个任务推到完结条件。

不在 pstack 里：

- `/deslop`、`control-cli` 和 `control-ui` 随 `cursor-team-kit` 插件提供。
- `/loop` 和 `/create-skill` 是 Cursor 内置的。
- pstack 没有 `/orchestrate` 这个 skill。Orchestrate 是 `/poteto-mode` 的一个 playbook。斜杠菜单里若出现 `/orchestrate`，那是别的插件提供的。

## playbook 和原则

playbook 是 `/poteto-mode` 里面的步骤清单，不是 skill，所以没有斜杠命令。在 `/poteto-mode` 里，描述任务就会匹配一个。下面这些说法会直接点名某一个：

- 「babysit this pr」或「check on pr 123」跑 Babysit。它把 PR 推到可以合并就停。除非对方要求 merge、land 或 ship，否则它不合入。
- 「land the stack」跑 Shipping。
- 「take over this branch」跑 Session pickup。
- 「pause safely」跑 Pause safely。
- 「full autopilot on this queue」跑 Autopilot-full。「stack them, don't ship」跑 Autopilot-stack。
- 「run the eval playbook」跑 Eval。

没有 `/poteto-mode` 时，「babysit this pr」这类说法可能启动 Cursor 自带的、干同一件事的 skill。[`poteto-mode`](../poteto-mode/SKILL.md) 的 Playbooks 一节列出了每个 playbook，以及什么时候用。[验证结果并开 PR](../../guide/06-verify-and-ship.md) 讲开 PR、babysit 和落地。

pstack 没有做计划的 skill。Cursor 的 Plan Mode 可以和它一起用。跨阶段或叠放 PR 的工作，向 `/poteto-mode` 要一份计划，会跑 [Multi-phase plan playbook](../poteto-mode/playbooks/multi-phase-plan.md)。它只写计划，不实现。设计问题，先用 Prototype playbook 或 `/architect` 在代码里定下来。

原则是一条规则一个 skill，`/poteto-mode` 会读它们，并在回复里点名。对方很少直接调用某一条。他们用原则名来转向，比如「apply prove it works. show me the real output.」。输入 `/principle-<name>` 仍然可以按需加载某一条。清单见 [用原则名来转向](../../guide/08-principles.md)。

## 修好跑偏的运行

| 现象 | 怎么办 |
|---|---|
| 聊了几轮以后，mode 不再生效 | 当时是按 Enter 启动的。改成 Custom Mode 启动，或每个任务都用 `/poteto-mode` 开头。 |
| 一个问题变成了上个任务的下一步 | 说「new task」，或者说这一轮不需要这个 mode。 |
| 新选的模型没有生效 | `/setup-pstack` 写出的 rule 从新聊天开始生效。开一个新的。 |
| 运行比预想更费 | 见「完成安装」里讲花费的那一段。 |
| 某个 skill 没有自己加载 | 只有 `/setup-pstack` 会从对方的话里自行加载。其余的要对方输入，或由 `/poteto-mode` 在步骤里跑，而且它不会跑遍每一个 skill。 |
| 并行的 agent 互相覆盖 | 给每个 agent 自己的 worktree（Git 的独立工作目录），或把它们当成 cloud agent 来跑，各用各的机器。 |
| 过夜运行有动静，但什么都没做完 | `/loop` 要的是一个能判定通过或失败的检查，不是一段时间。见 [睡觉时让工作继续跑](../../guide/07-overnight.md)。 |
| 回复拿构建变绿当成功 | 去要真实的命令、流程、存下来的值或性能剖析结果。这就是 prove-it-works 原则。 |

运行跑偏时，[`references/prompting.md`](./references/prompting.md) 里有一行纠偏。更多坑和值得照抄的配方，见 [配方与坑](../../guide/10-recipes-and-pitfalls.md)。

## 把 pstack 变成我的

- [`/automate-me`](../automate-me/SKILL.md) 从对方自己的历史起草一份个人 mode skill，和 `/poteto-mode` 一起用。
- 一次会话之后跑 [`/reflect`](../reflect/SKILL.md)，把它教到的东西写成对方批准的 skill 改动。
- `/poteto-mode write a skill for <workflow>` 跑编写 skill 的 playbook。Eval playbook 会盲测这次 skill 改动。
- 坏掉的 skill 单独开一个 PR 修，不要夹在它出错的那次功能工作里。

每一项见 [把它变成你的](../../guide/09-make-it-yours.md)。

## 怎么回复

先写答案。最多给一条示例提示词，放进代码块。对得上就从 [`references/recipes.md`](./references/recipes.md) 改一条，然后链到那一页。保持简短，除非对方要整张对照表。
