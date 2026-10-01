### 多阶段或多 PR 计划

**你负责计划，不负责写代码。计划是一份清单，由负责人逐项勾选，操作者依据证据审计。** 计划才是交付物。不要实施。

1. 若改动仅涉及一两个文件且方案明确，跳过计划。说明原因后停止。
2. 动笔前先通过原型解决未决问题。每个问题运行 `playbooks/prototype.md`。保留分支、SHA 及附录 A 的截图。仅就任何运行都无法定论的产品或偏好决策询问操作者。给出选项（**never-block-on-the-human** 原则 skill）。
3. 用 `subagent_type: "poteto-agent"` 在子代理中探索，并按 Subagents 章节为每个子代理指定模型（**guard-the-context-window** 原则 skill）。每个子代理返回文件指针、约定、测试命令和入口点。不要内联大段输出。
4. 将下方骨架复制到计划文件并填齐每个占位符。除非操作者指定路径，否则将文件写在 agent store 的 `docs/` 下。保持每个标题及每个子块的顺序不变。每个 PR 一节。一个 PR 即一项变更及其独立证据（**sequence-verifiable-units** 原则 skill）。在 **How to read this** 中命名执行 playbook。按 `playbooks/autopilot-stack.md` 末尾规则在 `playbooks/autopilot-full.md` 与 `playbooks/autopilot-stack.md` 之间选择。长期项目使用 `playbooks/orchestrate.md`。
5. 正文按 `/technical-writing` 完整撰写，再执行 `/unslop`。正文采用单一 Diátaxis 模式：how-to。附录承载 explanation 与 reference。每个标题陈述任务或结论。不用长破折号。不在句中使用冒号。
6. 运行 `node pstack/skills/poteto-mode/scripts/check-plan.mjs <plan.md>` 并修复其输出的每一行（**encode-lessons-in-structure** 原则 skill）。
7. 交回。发布计划路径与脚本输出后停止。仅在操作者明确同意后，按计划命名的执行 playbook 开始执行。

**验证。** 仅靠测试不足以完成验证。仅当某 PR 的 unit、live、perf 三项全部勾选时，该 PR 才算验证通过（**prove-it-works** 原则 skill）。该句即验证规则。每个验证块以此句开头。live 块为必填。在 PR head 上按 **swarm** skill 的 `swarm workers` 模型（默认 `grok-4.7-xhigh-fast`）通过对应 control skill 驱动真实界面，共十条 lane。每条 lane 对应一个勾选框，含具体场景、保存的截图及通过谓词。其中一条为 **Regression lane against trunk.** 在 trunk 与 head 上运行同一关键场景。若 trunk 尚无该功能，lane 记录该事实，并对 diff 新增的行为及用户等待的终态设门，而非虚构 trunk 结果。perf 门为双侧：trunk 与 head 均须产出命名指标。若 trunk 缺少该功能，还需隔离 diff 新增的工作，并为该工作及用户等待的端到端终态设定绝对预算。不要对不可比场景声称比率。perf 块须写明指标、交错探测、先测得的 trunk 基线，以及含失败阈值的规则。变更交互的 PR 须过 review 门：操作者在合并前于聊天中审阅截图与视频。未变更交互的 PR 写 `**Review gate.** None. <PR id> is not review-gated.`，其下无勾选框。

**Control skill。** 按界面选择。Browser、Electron 与 web UI 使用 `cursor-team-kit` 的 `control-ui`。CLI 与 TUI 使用 `cursor-team-kit` 的 `control-cli`。原生移动端使用仓库内驱动模拟器的 skill。触及两种界面的 PR 在两侧均设 lane。无 control skill 的界面记入附录 C 为风险，其 live 块仍须说明各 lane 如何驱动。

下面的代码块是要复制进计划文件的骨架。`check-plan.mjs` 按英文标题原文匹配，所以块内保持英文，不要翻译。阅读对照：

| 英文标题 | 在说什么 |
| --- | --- |
| How to read this | 怎么读这份计划。一格是一个工作单元，格里写明证据。 |
| Program checklist | 项目清单。 |
| Arm the program | 向操作者说明协议后停住，得到明确同意再启动 `/goal`。 |
| Spawn owners | 为每个 PR 指定负责人。 |
| PR mechanics | PR 怎么开、怎么叠。 |
| Verdict and merge | 谁给 verdict，谁合并。 |
| Boot recipe | 开跑时要读的文件和命令。 |
| Close the program | 收尾。 |
| Depends on. / Files. / Build. / You see. | 每个 PR 的依赖、文件、构建、操作者能看见什么。 |
| Verify, unit. / Verify, live. / Verify, perf. | 三项验证。live 必填。 |
| Review gate. / Merge. | 交互变更要人看截图。合并条件。 |

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
- [ ] On the operator's go, arm a `/goal` with this exact text. "<The plan path, the PR ids in order, the verification rule, who merges, and the done condition.>"
- [ ] Read these from trunk at program start. Re-read them at every tick.
  - [ ] `git show origin/main:pstack/skills/poteto-mode/playbooks/<execution playbook>.md`
  - [ ] `git show origin/main:pstack/skills/swarm/SKILL.md`
  - [ ] `git show origin/main:<control skill path>`
  - [ ] `git show origin/main:pstack/skills/poteto-mode/playbooks/opening-a-pr.md`
  - [ ] `git show origin/main:pstack/skills/<each other leaf skill the program uses>`
- [ ] Arm the 30-minute audit tick. In a local session, a real terminal `/loop`. In a cloud root, a cloud-sleeper wake chain. Never leave the cadence to memory.
- [ ] Use this tick prompt, verbatim. "Re-read the execution playbook from trunk and the armed /goal. Audit the operation against both and fix drift in this tick. Probe every active lane and judge progress by side effects only. Stand down a stuck lane and dispatch its replacement now. Then post a short status message to the operator in chat only when the audit found a tracked change that no earlier status message reported, such as a PR opened, a code-ready head, a round launched or closed, a verdict, a merge, a stuck agent and the action taken, a blocker added or cleared, or a decision only the operator can make. Name every such change and nothing else. Do not repeat a table, the merged list, or an unchanged blocker. If the audit found none, end the turn with no reply text. Either way, log this tick's row in your decision trail. The row names the items reported, or none."
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
- [ ] Open the PR ready, never draft, with `origin pr create --status open --base <base-branch>` or `gh pr create --base <base-branch>` according to the resolved forge. A stack child targets its parent branch.
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

**回复：** 计划路径、各 PR id 及其依赖与需 review 的集合、原型已证明与仍未证明的内容，以及检查脚本的输出。
