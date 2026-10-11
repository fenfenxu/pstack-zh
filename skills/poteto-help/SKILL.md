---
name: poteto-help
description: 引导用户做 pstack 的设置、使用 /poteto-mode，以及为一项任务挑选 skill、playbook 或原则。输入 /poteto-help，后面跟上问题。
disable-model-invocation: true
---

:::note[导读]
用 `/poteto-help` 问 pstack 怎么用。这页把问题转到对应的 skill、playbook 或指南，并给一段可以发出去的提示词。问的是用法，不要直接开工。
:::

# Poteto help

回答用户关于 pstack 的问题，交给用户一段可以发出去的提示词，并链到答案所在的文件。遇到用法问题，不要开工。用户问的是怎么做。跑一次 pstack 会花掉实打实的 token，所以让用户自己把提示词发出去。

一条消息如果是在派活，比如「use pstack to fix this bug」，那就不是用法问题。先读 [`poteto-mode`](../poteto-mode/SKILL.md)，按它把活做完，并提一次：Custom Mode 能让它保持开着。

本文件把问题转到真正写着答案的 skill 和指南页。细节以那些文件为准。引用之前，先读你转到的那一页。那一页和这张对照表不一致时，信那一页。这里的链接指向已安装的插件，用户未必打得开。把该文件的公开副本交给用户：`https://github.com/cursor/plugins/blob/main/pstack/` 再加上它的路径。

## 弄清用户要什么

从这条消息和整段对话推断用户要什么。已经点名场合的，比如「which skill reviews a PR?」，直接进对应那一节。还看不清，就问一道选择题，选项如下，然后只答用户选的那一节：

- 完成设置
- 用 `/poteto-mode` 开工
- 按场合挑一个 skill
- 修好跑偏的一次运行
- 把 pstack 变成我的

先核对会改变答案的状态，只有真会改变答案时才提：

- 没有 `~/.cursor/rules/pstack-models.mdc`，说明这个用户还没跑过 `/setup-pstack`，每个角色都在用默认模型。
- 项目里没有 `verify-*` skill，也没有别的应用 harness，agent 就没有脚本化的办法去驱动这个应用。问题如果是在问怎样证明一项改动有效，就提一下 `/create-verification-skill`。

模型 rule 不在，而且这件事要紧，就问用户要不要现在给每个角色挑一个模型，并定一个推理预算。用户是新人、问的是设置或花费，或者答案取决于跑哪些模型，这时就要紧。一次聊天最多问一次。如果用户要什么也还不清楚，就把两个问题一起问。给两个选项：

- 现在：把 `/setup-pstack` 交给用户去输入，同时回答用户的问题。
- 以后：先回答问题，并加一句：在用户跑 `/setup-pstack` 之前，每个角色都继续用默认模型。

## 完成设置

1. 在聊天里用 `/add-plugin pstack` 安装，或从侧栏的 Customize 安装。
2. 跑 [`/setup-pstack`](../setup-pstack/SKILL.md)。它会问推理预算，给每个角色指定一个模型，并写出一条 rule。这条 rule 从新聊天起生效。
3. 用 `/poteto-mode` 开始一项真实的任务，带上一个目标，和一个能分出通过或失败的检查。

装上以后，在用户调用某个 skill 之前，什么都不会变。只有 `/setup-pstack` 会从用户说的话里加载。细节见 [插件说明](../../guide/plugin-readme.md) 和 [安装 pstack](../../guide/01-setup.md)。主动提出按 [`references/prompting.md`](./references/prompting.md) 和用户一起写第一条提示词。

用户担心花费，就说清 token 花在哪，以及怎样少花 token。pstack 会在子代理和评审团上多花 token。再跑一次 `/setup-pstack`，选更小的预算或更便宜的模型。设成 `auto` 或 `inherit-parent` 的角色，用的是这场聊天的模型；聊天用 Auto 或更便宜的模型时，就能省 token。评审团名单越短，跑的子代理越少：名单里每一项跑一个子代理。把 `/poteto-mode` 留给需要严谨的工作。

pstack 是为 Cursor 做的。它的 skill 用的是 Agent Skills 格式，所以别的工具也能读。但大多数工作流 skill，包括 `/poteto-mode`、`/how`、`/why` 和 `/teach`，会按每个角色各自的模型拉起 Cursor 子代理，而 Custom Mode 和 `/loop` 又是 Cursor 的功能，所以这些部分到了别的工具里可能用不了。

## 用 `/poteto-mode` 开工

`/poteto-mode` 给任务匹配一个 playbook，把该 playbook 的步骤复制进 todo 列表，再按步骤的需要去跑其他 skill。它跳过的步骤仍留在列表里，写成 `skip: <reason>`。好的提示词会写清目标，以及怎样算做完。它不把 skill 逐个列出来，因为手写的顺序往往会漏掉或重排 playbook 本来会保留的步骤。帮用户写提示词之前，先读 [`references/prompting.md`](./references/prompting.md)。例子见 [把工作交给 `/poteto-mode`](../../guide/02-poteto-mode.md)。

`/poteto-mode` 会不会一直开着，取决于用户怎么启动它：

- 在 `/poteto-mode` 上按 Enter，这个 skill 只挂在这一条消息上。对话往前走，它就会淡掉。
- 在 Mac 上按 Option+Enter，在 Windows 上按 Alt+Enter，或从 skill 条目里选 Use as Mode，会把它做成 Custom Mode。用户退出这个 mode 之前，它每一轮都留在上下文里，闲聊的那些轮它不插手。
- Cursor 的文档把 Custom Mode 列在 Agents Window 和 CLI 里。在这两处之外，每个新任务都用 `/poteto-mode` 开头。

提到这一点时，链到 [Cursor 的 skills 文档](https://cursor.com/docs/skills)。聊到一半时，说「new task」会让这个 mode 重新匹配一个 playbook。`/poteto-mode` 已经用 `poteto-agent` 来拉起 playbook 步骤里的子代理。你自己拉起子代理、也想要同样的风格时，用 `subagent_type: "poteto-agent"` 来拉。

## 挑一个 skill

默认答案是 `/poteto-mode`。它的步骤需要时，会跑大多数其他 skill。用户想要的比 playbook 给出的更多或更少时，再直接点名一个 skill。推荐之前先读那个 skill，并给一条示例提示词。

| 用户想做的事 | skill |
|---|---|
| 严谨地做任何不简单的任务 | [`/poteto-mode`](../poteto-mode/SKILL.md) |
| 弄清代码现在怎么运作，或新代码该放在哪 | [`/how`](../how/SKILL.md) |
| 弄清代码为什么是这个形状，或某个数字是从哪来的 | [`/why`](../why/SKILL.md) |
| 用大白话弄懂一次改动或一个子系统 | [`/teach`](../teach/SKILL.md) |
| 补上自己最近在某个话题上的进度 | [`/recall`](../recall/SKILL.md) |
| 补上这次对话里、自己上一条消息之后的一切，上下文要够对每件待用户处理的事做决定 | [`/ketchup`](../ketchup/SKILL.md) |
| 弄清一小段 diff 可能在自身以外弄坏什么 | [`/blast-radius`](../blast-radius/SKILL.md) |
| 动手写跨函数边界的代码之前，先定下类型和模块形状 | [`/architect`](../architect/SKILL.md) |
| 对同一份简报做几次尝试，再合成最好的一份 | [`/arena`](../arena/SKILL.md) |
| 按切片做并行检查，或让 worker 竞速，并且都用 cloud agent 来跑 | [`/swarm`](../swarm/SKILL.md) |
| 让不同的模型审一份 diff，并试着把它挑破 | [`/interrogate`](../interrogate/SKILL.md) |
| 有成本低的本地测试时，先写测试再修 bug | [`/tdd`](../tdd/SKILL.md) |
| 把 TypeScript 规则用到 `.ts` 或 `.tsx` 的工作上 | [`/typescript-best-practices`](../typescript-best-practices/SKILL.md) |
| 审查之前去掉注释，并且用一个没写这些注释的审查者 | [`/no-comments`](../no-comments/SKILL.md) |
| 清掉书面文字里的 AI 痕迹 | [`/unslop`](../unslop/SKILL.md) |
| 按标准写文档、RFC、README、PR 描述或 commit 信息 | [`/technical-writing`](../technical-writing/SKILL.md) |
| 用大白话再听一遍上一条回复 | [`/bro`](../bro/SKILL.md) |
| 给 agent 一种脚本化的办法，用来驱动应用并证明行为 | [`/create-verification-skill`](../create-verification-skill/SKILL.md) |
| 让验证 skill 和它的 feature map 重新与应用对齐 | [`/maintain-verification-skill`](../maintain-verification-skill/SKILL.md) |
| 汇报一个性能数字或按它行动之前，先把这个数字核一遍 | [`/benchmark-checklist`](../benchmark-checklist/SKILL.md) |
| 跑一次大改或横切改动，或跑一次离开一阵再回来审的改动 | [`/figure-it-out`](../figure-it-out/SKILL.md) |
| 一次运行期间留下决策日志，事后再审这份日志 | [`/show-me-your-work`](../show-me-your-work/SKILL.md) |
| 为每个角色挑一个模型，并定一个推理预算 | [`/setup-pstack`](../setup-pstack/SKILL.md) |
| 把自己的工作习惯做成一份个人 mode skill | [`/automate-me`](../automate-me/SKILL.md) |
| 把做完的任务里学到的东西写成 skill 改动 | [`/reflect`](../reflect/SKILL.md) |
| 别让 agent 在这个仓库里重复同样的错误 | [`/correct`](../correct/SKILL.md) |
| 做一页带按钮的页面，按钮经 webhook 唤醒 Grok Bot | [`/make-bot-ui`](../make-bot-ui/SKILL.md) |
| 在 pstack 里找路 | `/poteto-help` |

和本文件同级的某个 skill 目录如果没出现在这张表里，就读它的 frontmatter，按 `description` 来转。`principle-*` 目录见下面的原则一节。

不好分的时候：

- `/how` 解释代码在做什么。`/why` 解释原因。`/teach` 会跑其中一个或两个都跑，再用大白话解释结果。
- `/arena` 给每个 worker 同一份简报，再把最好的部分合成起来。`/swarm` 把工作拆成切片或一场竞速，交回一份报告。
- `/architect` 一定下设计就实现。加上「with checkpoint」，就可以在它写代码之前先审设计。
- `/interrogate` 审这份 diff。`/blast-radius` 寻找 diff 以外会坏的地方，并证明那一件能使这次改动安全的事实。
- `/recall` 跨最近的多次聊天重建上下文。`/ketchup` 只覆盖这一次对话里、用户上一条消息之后的部分。要接着某一个具体的聊天或分支做下去，用的是 Session pickup playbook。
- `/figure-it-out` 设计一次严谨的运行。Orchestrate playbook 跑一项跨越多天、许多 PR 的项目。Autonomous run playbook 把一个任务推进到完成条件。

不在 pstack 里：

- `/deslop`、`control-cli` 和 `control-ui` 随 `cursor-team-kit` 插件提供。
- `/loop` 和 `/create-skill` 是 Cursor 内置的。
- pstack 没有 `/orchestrate` 这个 skill。Orchestrate 是 `/poteto-mode` 的一个 playbook。斜杠菜单里如果出现 `/orchestrate`，那是别的插件提供的。

## playbook 和原则

playbook 是 `/poteto-mode` 里面的步骤清单，不是 skill，所以没有斜杠命令。在 `/poteto-mode` 里面，把任务描述出来就会匹配到一个。下面这些说法会直接点名某一个：

- 「babysit this pr」或「check on pr 123」会跑 Babysit。它把 PR 推进到 merge-ready 就停。除非用户要求 merge、land 或 ship，否则它不会合并。
- 「land the stack」跑 Shipping。
- 「take over this branch」跑 Session pickup。
- 「pause safely」跑 Pause safely。
- 「full autopilot on this queue」跑 Autopilot-full。「stack them, don't ship」跑 Autopilot-stack。
- 「run the eval playbook」跑 Eval。

没有 `/poteto-mode` 时，「babysit this pr」这样的说法，启动的会是 Cursor 自带的、做同一件事的 skill，而不是这里的 playbook。[`poteto-mode`](../poteto-mode/SKILL.md) 的 Playbooks 一节列出每个 playbook，以及各自什么时候用。[验证结果并开 PR](../../guide/06-verify-and-ship.md) 讲如何开 PR、如何 babysit，以及如何 land 一个 PR。

pstack 没有做计划的 skill。Cursor 的 Plan Mode 跟它配合着用。工作如果跨多个阶段，或是叠放的 PR，向 `/poteto-mode` 要一份计划，跑的就是 [Multi-phase plan playbook](../poteto-mode/playbooks/multi-phase-plan.md)。那个 playbook 写计划，不实现。设计上的问题，先用 Prototype playbook 或 `/architect`，在代码里定下来。

原则是一条规则一个 skill。`/poteto-mode` 会读它们，并在回复里点出它们。用户很少调用某一条，而是用原则名来转向，例如「apply prove it works. show me the real output.」。输入 `/principle-<name>` 仍然可以按需加载某一条。清单见 [用原则名来转向](../../guide/08-principles.md)。

## 修好跑偏的一次运行

| 现象 | 怎么办 |
|---|---|
| 聊了几轮以后，mode 不再起作用 | 它是用 Enter 启动的。改成用 Custom Mode 启动，或每个任务都用 `/poteto-mode` 开头。 |
| 一个问题被当成了上一件任务的下一步 | 说「new task」，或说明这一轮不需要这个 mode。 |
| 新选的模型没有生效 | `/setup-pstack` 写出的 rule 从新聊天起生效。另开一个新聊天。 |
| 运行比预期更费 | 见「完成设置」里讲花费的那一段。 |
| 某个 skill 没有自行加载 | 只有 `/setup-pstack` 会从用户说的话里加载。其余的要用户输入，或由 `/poteto-mode` 去跑；而它并不会跑每一个 skill。 |
| 并行的 agent 互相覆盖 | 给每个 agent 一个自己的 worktree，或把它们作为 cloud agent 来跑。这样各自有一台机器。 |
| 过夜运行有动静，但什么都没做完 | `/loop` 要的是一个能分出通过或失败的检查，不是一段时长。见 [睡觉时让工作继续跑](../../guide/07-overnight.md)。 |
| 回复把构建变绿当成成功 | 去要真实的命令、流程、存下来的值，或性能剖析。这就是 prove-it-works 原则。 |

运行跑偏时，[`references/prompting.md`](./references/prompting.md) 里有一行纠偏。更多的坑，以及值得照抄的配方，见 [配方与坑](../../guide/10-recipes-and-pitfalls.md)。

## 把 pstack 变成我的

- [`/automate-me`](../automate-me/SKILL.md) 根据用户自己的历史起草一份个人 mode skill，用来和 `/poteto-mode` 一起用。
- 一次会话之后跑 [`/reflect`](../reflect/SKILL.md)，把这次会话的教训写成 skill 改动，由用户批准。
- `/poteto-mode write a skill for <workflow>` 会跑 authoring playbook。eval playbook 对 skill 改动做盲测。
- 行为不对的 skill 放在它自己的 PR 里修，不要放进它出错的那次功能工作里。

每一项见 [把它变成你的](../../guide/09-make-it-yours.md)。

## 怎么回复

答案写在前面。示例提示词最多一条，放在代码块里。有合适的，就从 [`references/recipes.md`](./references/recipes.md) 改一条，然后附上该文件的链接。保持简短，除非用户要的是整张对照表。
