### 多阶段或多 PR 计划

**计划归你负责，代码不归你。计划是一份清单。负责人一格一格地勾，操作者凭证据审核。** 交付物就是计划。不要动手实现。

1. 改动只涉及一两个文件、做法也一目了然时，跳过计划。说明这一点，然后停下。
2. 动笔之前，先用原型把悬而未决的问题定下来。每个问题跑一遍 `playbooks/prototype.md`。留好分支、SHA 和截图，写进附录 A。只有任何运行都定不下来的产品或偏好问题，才去问操作者，并给出选项（**never-block-on-the-human** 原则 skill）。
3. 探索交给子代理。用 `subagent_type: "poteto-agent"`，并按 Subagents 一节为每个子代理明确指定模型（**guard-the-context-window** 原则 skill）。每个子代理交回文件指针、约定、测试命令和入口。不要把大段原始内容直接贴回来。
4. 把下面的骨架复制进计划文件，填满每个占位符。操作者没指定路径时，把文件写到 agent 存储目录的 `docs/` 下。每个标题、每个子块都要保留，顺序和骨架一致。一个 PR 一节。一个 PR 就是一项改动，带着它自己的证据（**sequence-verifiable-units** 原则 skill）。在 **How to read this** 里写明执行时用哪个 playbook。`playbooks/autopilot-full.md` 和 `playbooks/autopilot-stack.md` 二选一，依据 `playbooks/autopilot-stack.md` 末尾的规则。长期运行的项目用 `playbooks/orchestrate.md`。
5. 全文严格按 `/technical-writing` 写，写完再跑 `/unslop`。正文只用一种 Diátaxis 模式，就是 how-to。explanation 和 reference 放进附录。每个标题直接写出任务或结论。不用长破折号。句子中间不用冒号。
6. 运行 `node pstack/skills/poteto-mode/scripts/check-plan.mjs <plan.md>`，把它打印出来的每一行都修掉（**encode-lessons-in-structure** 原则 skill）。
7. 交回。贴出计划路径和脚本输出，然后停下。操作者明确说开始之后，才按计划里写明的执行 playbook 开始执行。

**验证。** 光有测试不算充分的验证。只有 unit、live、perf 三类勾选框全部勾上，一个 PR 才算验证通过（**prove-it-works** 原则 skill）。这句话就是验证规则。每个验证块都以这句话开头。live 块必须有。在 PR head 上开十条 lane（swarm 里并行跑的验证线，一条一个场景），按 **swarm** skill 的做法，用 `swarm workers` 那一项的模型（默认 `grok-4.7-xhigh-fast`），通过该界面的 control skill 操作真实界面。每条 lane 是一个勾选框，写明具体场景、要保存的截图和通过条件。其中一条是 **Regression lane against trunk**（对照 trunk 的回归 lane）。它在 trunk 和 head 上跑同一个关键场景。如果 trunk 还没有这个功能，这条 lane 就记下这个事实，改为给 diff 新增的行为、以及用户最终等到的状态设门禁，不要编一个 trunk 结果出来。perf 门禁两边都要测。trunk 和 head 都必须产出指定的指标。如果 trunk 没有这个功能，还要把 diff 新增的那部分工作单独拿出来，为这部分工作和用户等待的端到端状态设定绝对预算。不同的场景之间不要算比值。perf 块要写明指标、交错运行的探测、先在 trunk 上测出的基线，以及判定规则和判为失败的数值。改动了交互的 PR 要过评审门禁。合并之前，操作者在聊天里看截图和视频，做评审。没有改动交互的 PR 写 `**Review gate.** None. <PR id> is not review-gated.`，下面不放勾选框。

**选哪个 control skill。** 按界面选。浏览器、Electron 和 web 界面用 `cursor-team-kit` 的 `control-ui`。CLI 和 TUI 用 `cursor-team-kit` 的 `control-cli`。原生移动端用仓库里现有的驱动模拟器的 skill。一个 PR 碰到两种界面，两边都要开 lane。没有 control skill 的界面算一项风险，写进附录 C。它的 live 块仍要写明每条 lane 怎么操作它。

下面的代码块是要复制进计划文件的骨架。`check-plan.mjs` 按英文标题原样匹配，所以块里保持英文，不要翻译。各个英文标题的意思见下表。

| 英文标题 | 在说什么 |
| --- | --- |
| How to read this | 怎么读这份计划。一个勾选框是一个工作单元，每个框写明勾上它需要什么证据。 |
| Program checklist | 整个项目的总清单。 |
| Arm the program | 先向操作者说明做法，然后停下。得到明确同意后，用 `/loop 1h` 设好每小时一次的巡检。 |
| Spawn owners | 每个 PR 派一名负责人，写明依赖顺序、文件边界和评审门禁。 |
| PR mechanics | 每个 PR 怎么开、怎么推、什么时候 rebase。 |
| Verdict and merge | 怎么跑 swarm 拿到 verdict（验证结论），什么条件下才能合并。 |
| Boot recipe | 每条 live lane 怎么在 PR head 上把应用跑起来、怎么截图。 |
| Close the program | 收尾。确认每个框都有证据，再按执行 playbook 给操作者回复。 |
| Depends on. / Files. / Build. / You see. | 每个 PR 依赖谁、动哪些文件、做什么改动、做完能看到什么。 |
| Verify, unit. / Verify, live. / Verify, perf. | 三类验证，分别是单元测试、真实界面上的十条 lane、trunk 与 head 的性能对比。live 必须有。 |
| Review gate. / Merge. | 改了交互的 PR 要等操作者看过截图和视频再合并。合并前要满足的条件。 |

````markdown
# <Program> plan

<Under ten lines. What changes, for whom, the rule the program enforces, and the PR ids in order.>

## How to read this

One box is one unit of work. Every box names the evidence that checks it. A nested box is a sub-step of the box above it. Check a box only when its evidence exists, a file, a log line, a screenshot, a test run, or a SHA. The body is a how-to. The appendices explain and record.

The program runs `pstack/skills/poteto-mode/playbooks/<execution playbook>.md`. <Who merges, and which PR ids are the operator's items that stop at merge-ready.>

Tests alone are not sufficient verification. A PR is verified only when its unit, live, and perf boxes are all checked.

## Program checklist

### Arm the program

- [ ] State the protocol and this plan to the operator, then stop. Start execution only on the operator's explicit go.
- [ ] Read these from trunk at program start. Re-read them at every tick.
  - [ ] `git show origin/main:pstack/skills/poteto-mode/playbooks/<execution playbook>.md`
  - [ ] `git show origin/main:pstack/skills/swarm/SKILL.md`
  - [ ] `git show origin/main:<control skill path>`
  - [ ] `git show origin/main:pstack/skills/poteto-mode/playbooks/opening-a-pr.md`
  - [ ] `git show origin/main:pstack/skills/<each other leaf skill the program uses>`
- [ ] On the operator's go, arm the audit tick as `/loop 1h` with the tick prompt below. Never leave the cadence to memory.
- [ ] Use this tick prompt, verbatim. "Re-read the execution playbook from trunk. Audit the operation against it and fix drift in this tick. Probe every active lane and judge progress by side effects only. Stand down a stuck lane and dispatch its replacement now. Then post a short status message to the operator in chat only when the audit found a tracked change that no earlier status message reported, such as a PR opened, a code-ready head, a round launched or closed, a verdict, a merge, a stuck agent and the action taken, a blocker added or cleared, or a decision only the operator can make. Name every such change and nothing else. Do not repeat a table, the merged list, or an unchanged blocker. If the audit found none, end the turn with no reply text. Either way, log this tick's row in your decision trail. The row names the items reported, or none."
- [ ] On the operator's hold or stand-down, send every owner a zero-writes order at once.

### Spawn owners

- [ ] Spawn one owner per PR with the full lifecycle the execution playbook names.
- [ ] Follow this dependency graph. Start dependent work only after its parent merges, or base it on the parent branch when the execution playbook stacks.
  - [ ] <PR id> and <PR id> are independent and first. Both branch from `main`.
  - [ ] <PR id> after <PR id>.
- [ ] Hold the file boundaries. <PR id or class> touches only `<glob>`.
- [ ] Hold the review gate. <PR ids> change an interaction. They wait for the operator's review in chat with screenshots and a video before merge.

### PR mechanics, for every PR

- [ ] Resolve the forge once. Default to `gh`; if `command -v origin` succeeds and Origin can resolve the repository, use `origin pr` for every PR operation. Record any fallback to `gh`. Never require `gt`.
- [ ] Open the PR ready, never draft, per **Opening a PR**. Use the run's built-in PR tool when it has one, else `origin pr create --status open --base <base-branch>` or `gh pr create --base <base-branch>` according to the resolved forge. A stack child targets its parent branch.
- [ ] Run the repo's lint and typecheck once before the PR-facing push. Push with hooks on.
- [ ] Run `/deslop` before each commit and `/no-comments` before review.
- [ ] Triage every Bugbot and security-reviewer comment per `../references/bugbot-triage.md`.
- [ ] Rebase onto current trunk before the code-ready report and babysit. Keep that merge base in fix rounds. Rebase again only at merge prep, on a `git merge-tree` conflict with trunk, or on a CI failure that comes from a change on trunk.

### Verdict and merge, for every PR

- [ ] At the code-ready head SHA and at each later push that changes the patch, run the swarm per `pstack/skills/swarm/SKILL.md`. One gates lane. The ten live lanes from the PR's **Verify, live** block. The perf lane from its **Verify, perf** block. Two or more audit lanes, each with its own focus, that read the diff and the receipts and distrust the PR body. The root audits the receipts in the merge-ready report before the verdict.
- [ ] Clean only when every lane is `PASS`. Findings go back to the owner, including a defect that a lane filed as a note. A new head gets a fresh swarm and a fresh verdict, except for results that stay valid under the patch-id rule in `playbooks/shipping.md`.
- [ ] <The merge or append rule from the execution playbook, with the patch-id rule from `playbooks/shipping.md`.>

### Boot recipe, for every live lane

Each live lane runs on its own cloud VM at the PR head. Drive through `control-ui` or `control-cli` from `cursor-team-kit`.

- [ ] `git fetch origin <head-branch> && git checkout <head SHA>`.
- [ ] <Start the backend and the surface. Wait for ready.>
- [ ] <Deliver input only through the control skill's commands. Name the read-only diagnostics.>
- [ ] Save every screenshot to `/tmp/swarm-<pr-id>/worker-<n>/<slug>.png` and return the paths with the report.

## <Task as a verb phrase> (<PR id>)

**Depends on.** <PR id, or None.>

**Files.**

- [ ] Edit `<path>`.
- [ ] Create `<path>`.
- [ ] Delete `<path>`.

**Build.**

- [ ] <One change. Name the symbol and the file.>

**You see.**

- [ ] <One observable result, with the exact log line or screen state.>

**Verify, unit.** Tests alone are not sufficient verification. A PR is verified only when its unit, live, and perf boxes are all checked.

- [ ] <Test file and the case it gains.> Run `<command>`.

**Verify, live.** Tests alone are not sufficient verification. A PR is verified only when its unit, live, and perf boxes are all checked. Ten lanes on `<swarm workers model>` at the PR head, per the boot recipe.

- [ ] Lane 1. Regression lane against trunk. Run <the same load-bearing scenario> at trunk and head. If trunk lacks the feature, record that and gate <the behavior the diff adds plus the end state the user waits for>. Save `<slug>.png`. Pass when <predicate>.
- [ ] Lane 2. <Scenario.> Save `<slug>.png`. Pass when <predicate>.
- [ ] Lane 3. <Scenario.> Save `<slug>.png`. Pass when <predicate>.
- [ ] Lane 4. <Scenario.> Save `<slug>.png`. Pass when <predicate>.
- [ ] Lane 5. <Scenario.> Save `<slug>.png`. Pass when <predicate>.
- [ ] Lane 6. <Scenario.> Save `<slug>.png`. Pass when <predicate>.
- [ ] Lane 7. <Scenario.> Save `<slug>.png`. Pass when <predicate>.
- [ ] Lane 8. <Scenario.> Save `<slug>.png`. Pass when <predicate>.
- [ ] Lane 9. <Scenario.> Save `<slug>.png`. Pass when <predicate>.
- [ ] Lane 10. <Scenario.> Save `<slug>.png`. Pass when <predicate>.

**Verify, perf.** Tests alone are not sufficient verification. A PR is verified only when its unit, live, and perf boxes are all checked.

- [ ] Metric. <What is measured at both trunk and head. If trunk lacks the feature, also name the diff-added work and the end-to-end state the user waits for.>
- [ ] Probe. <The command or procedure, run at trunk and at the head, interleaved. Both sides must produce the metric.>
- [ ] Baseline. Record the trunk <value> first.
- [ ] Rule. <Head against trunk, with the number that fails. If the scenarios differ, add absolute budgets for the diff-added work and the user-visible end state instead of an invalid ratio.>

**Review gate.** The operator reviews before merge.

- [ ] Copy lane <n> screenshots into `<media path>/<pr-id>-review-<slug>.png`.
- [ ] Record a 30 to 60 second video of the change on a lane VM. Save it as `<media path>/<pr-id>-review.mp4`.
- [ ] Post the screenshots and the video in chat. Stop at merge-ready. Wait for the operator's click.

**Merge.**

- [ ] Root's clean verdict at the exact head SHA.
- [ ] Bugbot triage done.
- [ ] Rebased onto current trunk after the verdict, patch-id unchanged.
- [ ] <The owner squash-merges its own PR, or the root appends it to the base-branch stack and the operator lands it bottom-up.>

## Close the program

- [ ] Every box above is checked with its evidence.
- [ ] Reply to the operator with the report the execution playbook names.

## Appendix A. Prototype evidence

<Each open question a prototype answered, with the branch, the SHA, and the artifact links. Each question that stays unproven.>

## Appendix B. Alternatives rejected

<Each approach weighed and why it lost.>

## Appendix C. Risks

<Each risk with the PR it lands in and what the owner watches.>

## Appendix D. Links and reading list

<Docs to read before editing. Which PRs get `pstack/skills/how/SKILL.md` and `pstack/skills/interrogate/SKILL.md`. The trail per `pstack/skills/show-me-your-work/SKILL.md`.>
````

**回复：** 计划路径；各个 PR id、它们的依赖，以及需要过评审门禁的那几个；原型证明了什么，还有什么没证明；检查脚本的输出。
