### Opening a PR

在每个其他 playbook 末尾调用。

**Worktree。** 从 main 的 git worktree 工作。子代理继承该 worktree。同一分支上多次 `Task` 调用时，各用独立 worktree，或在它们之间执行 `git fetch && git reset --hard origin/<branch>`。分支脏且含无关工作时：patch 出去，新建 worktree，再 apply。worktree 纠缠时：从 main reset，最小重做。

**Commits。** 可频繁 commit。开 PR 前 rebase 成小而有序的 commit。每个 commit 对应未来一个 PR：可落地、顺序能讲清故事。修复属于刚做的 commit 时用 amend。可分离时用新 commit。

**PRs。** commit 前用 `cursor-team-kit` 的 `/deslop` 处理 diff。review 前运行 `/no-comments`。每个 PR 标题、PR 描述与 commit 正文用 `/technical-writing` 撰写，再应用 `/unslop`。应用 technical-writing 各层，Diátaxis 除外。每个动作用一个词，保留冠词，能用 plain verb 时避免 `-ing`。

**Titles。** 使用 Conventional Commits 形式 `type(scope): subject`。type 用 `feat`、`fix`、`docs`、`refactor`、`test`、`chore` 或 `perf`。scope 用变更区域，如 `pstack` 或 `poteto-mode`。subject 短且祈使。有承载变更的真实 symbol 时写出。例如 `fix(pstack): retarget opening-a-pr babysit trigger`。不要加句末句号。

**Descriptions。** PR 正文是简报，不是实验笔记本。已有 diff 的 reviewer 应从中了解为何变更、范围外是什么、如何证明变更有效。squash commit 正文即 PR 正文。若正文会使 squash commit 超过约 40 行，删减正文。

按以下顺序使用各节。无内容可写时省略该节。

- `## Why`。用一两段短句说明意图与方案。不要列 SHA 或 rebase 谱系。不要加「based on main」前言。
- `## Scope`。用 bullet 列出真实 symbol 与 path。rename 或 retarget 时写出两侧。仅边界重要时说明范围内外。不要写逐文件长文。
- `## Tradeoffs`。只写 reviewer 否则会问的已否决备选。无真实取舍时跳过。
- `## Blast Radius`。一至三句说明影响谁或什么，以及变更安全或风险的原因。说明 main 若不合并 fix 的持续代价。
- `## Verification`。写出每条真实运行路径及结果。性能变更用 `before → after` 形式报告一个带单位的主指标。其余证据链接 arena 或 swarm 目录。不要写样本量方法论、swarm 复述或指标表。

上述各节之后，当视频或截图能证明主张时再附上。不要粘贴完整 SHA、swarm 或 arena lane 复述、杠杆修正长文、逐文件 checklist 或「CLEAN」裁决。这些细节放在链接产物中。不要用 `## Summary` 或 `## Test plan` 套话。commit 正文不重复 subject。

**Forge。** 第一次 PR 操作前解析 forge，create、edit、view、watch、merge 全程保持该选择。GitHub CLI（`gh`）为默认。若 `command -v origin` 成功且 Origin 能解析仓库，优先 `origin pr ...`。Origin 不存在或无法解析仓库时继续用 `gh` 并记录回退。不要要求 Graphite（`gt`）。

**规模与栈。** 优先五个窄 PR，而非一个大 PR。栈是 base-branch 链。root PR 指向 trunk。每个子分支 rebase 到父分支精确 tip，其 PR 指向父分支。按已解析 forge 用 `origin pr create --status open --base <parent-branch>` 或 `gh pr create --base <parent-branch>` 创建子 PR。用 `origin pr edit <pr> --base <parent-branch>` 或 `gh pr edit <pr> --base <parent-branch>` retarget 已有子 PR。独立工作才从 trunk 分支。大规模栈工作前在 trunk 上 rebase。

**Readiness.** 每个 PR 以 ready 状态打开，不要 draft。Origin 传 `--status open`。`gh` 省略 `--draft`。Cloud-agent PR 工具默认 draft，因此每次创建 PR 调用设 `draft: false`。若 PR 仍以 draft 打开，按已解析 forge 运行 `origin pr ready <number>` 或 `gh pr ready <number>`。引用 PR 状态前先运行 `origin pr view <number>` 或 `gh pr view <number>`。

**Babysit.** 开 PR 不会启动 babysit。发布 URL 后继续构建。先完成阶段或整栈。仅当整栈存在后用户另行要求时，才单独跑 babysit。每个新 PR 都 babysit 会拖慢构建，并在后续波次重启 check 上浪费资源。反馈偏离意图时予以推回。

开 PR 的子代理运行 `interrogate`、`/deslop`、`/no-comments` 并发布 URL。然后回到父代理，不进行 babysit，除非它是 Autopilot-full 或 Autopilot-stack owner。该 owner 的任务书分配 babysit 循环，即 `playbooks/babysit.md` 所等待的请求。owner 在 code-ready 报告后启动循环，并按其 playbook 报告 merge-ready 或 STACK-READY。此处与 `playbooks/babysit.md` 中「整栈建完再 babysit」的规则不适用于该 owner。
