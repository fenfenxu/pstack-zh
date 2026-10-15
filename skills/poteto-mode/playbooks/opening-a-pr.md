### Opening a PR

其他每个 playbook 结束时都会调用它。

**Worktree。** 在从 main 切出来的 git worktree 里工作。子代理沿用这个 worktree。同一个分支上的多次 `Task` 调用，要么各用一个 worktree，要么在两次调用之间执行 `git fetch && git reset --hard origin/<branch>`。分支脏了、混着无关的改动时，先导出 patch，再开一个新的 worktree，把 patch 应用进去。worktree 乱成一团时，从 main 重置，只做最少的重做。

**提交。** 勤提交。开 PR 之前，rebase 成小而有序的 commit。每个 commit 都是将来的一个 PR，要能单独落地，顺序要能把来龙去脉讲清楚。修复本该属于刚做的那个 commit 时，用 amend。能分开的，就新开一个 commit。commit 正文不要重复标题行。

**PR。** commit 之前用 `cursor-team-kit` 里的 `/deslop` 把 diff 过一遍，评审之前跑 `/no-comments`。每个 PR 标题、描述和 commit 正文都用 `/technical-writing` 来写（除 Diátaxis 以外的每一层都要用，并套用 STE 的用词规则：每个动作一个词，保留冠词，能用简单动词就不用 `-ing`），再用 `/unslop` 处理一遍。

**标题。** Conventional Commits 格式 `type(scope): subject`，末尾不加句号。type 例如 `feat`、`fix`、`docs`、`refactor`、`test`、`chore` 或 `perf`。scope 是改动的区域，例如 `pstack` 或 `poteto-mode`。subject 要短，用祈使语气，改动落在某个真实的符号上时写出这个符号，例如 `fix(pstack): retarget opening-a-pr babysit trigger`。

**描述。** PR 正文是给手里已经有 diff 的评审者看的一分钟简报，不是实验记录本。它要说清为什么要改、哪些没包括、可能弄坏什么、你怎么证明它能用。句子要短、要简单，少用标识符，不要堆大段文字。正文会变成 squash commit 的正文，所以把这个 commit 控制在大约 40 行以内。用 `##` 标题，不要用加粗的引导语，顺序如下。某一节没东西可写，就去掉：

- `## Why`：问题和做法，一到三个短句。不要写 SHA、rebase 的来龙去脉，或「based on main」这类开场白。
- `## What changed`：一到三条简短的要点。只有某个真实的符号或路径承载了这次改动，才写出它。重命名或改指向时，新旧两边都写出来。
- `## Scope`：一定要有。一到三条短项，写它涵盖什么、刻意不包括什么（后续工作、已知缺口）。不要写符号、路径，也不要逐个文件写长文。
- `## Tradeoffs`：只写那些你放弃了、评审者本来会追问的备选方案。没有真正可选的余地时，跳过。
- `## Blast Radius`：一两句话，说明这次改动波及谁或什么，以及为什么安全或有风险。如果 main 是红的，写明让它继续红着的代价。
- `## Verification`：一到三条要点，每条是一条真实跑过的路径和它的结果。性能改动只报一个主要数字，带单位，写成 `before → after`，其余证据链接到 arena 或 swarm（一批并行的子代理）的目录。不要写样本量的方法论、swarm 过程复述或指标表格。

这些节之后，如果视频或截图能证明某个说法，就附上。完整的 SHA、swarm 或 arena 各条 lane（并行执行的一路）的过程复述、修正工具的长篇说明、逐文件的清单，以及「CLEAN」这类 verdict（判定结果），放进单独的产物再附上链接，不要写进正文。

**托管平台。** 第一次操作 PR 之前，先确定用哪个代码托管平台。之后创建、编辑、查看、监视和合并都沿用这个选择。默认用 GitHub CLI（`gh`）。如果 `command -v origin` 成功，并且 Origin 能识别这个仓库，优先用 `origin pr ...`。如果没有 Origin，或者它识别不了这个仓库，就继续用 `gh`，并记下这次回退。不要把 Graphite（`gt`）当成必需。

**内置 PR 工具。** 这次运行提供内置 PR 工具时，创建、编辑、改目标分支、标为就绪，都用它做，绝不用托管平台的命令行。用命令行开的 PR 会漏掉这个工具跟踪的东西。工具管不到的操作，以及这次运行根本没有这种工具时的所有 PR 操作，都用已经确定的托管平台。

**规模与 stack。** 宁可开五个窄 PR，也不要开一个大 PR。stack 是一条靠目标分支串起来的链。根 PR 以 trunk 为目标。每个子分支都准确地 rebase 到父分支的最新提交上，它的 PR 以父分支为目标。父 PR 一合并，就按 `playbooks/shipping.md` 第 4 步把它的子 PR 挪到 trunk 上。没有内置 PR 工具时，按已确定的托管平台，用 `origin pr create --status open --base <parent-branch>` 或 `gh pr create --base <parent-branch>` 创建子 PR，用 `origin pr edit <pr> --base <parent-branch>` 或 `gh pr edit <pr> --base <parent-branch>` 改一个已有子 PR 的目标分支。只有独立的工作，才从 trunk 拉分支。在 stack 上做较大的工作之前，先 rebase 到 trunk 上。

**就绪状态。** 每个 PR 打开时都处于就绪状态，绝不开成草稿。内置 PR 工具可能默认开草稿，所以每次用它创建 PR，都要设 `draft: false`。用 Origin 时，传 `--status open`。用 `gh` 时，不加 `--draft`。如果 PR 还是以草稿打开，就用 PR 工具把它标为就绪，或者按已确定的托管平台运行 `origin pr ready <number>` 或 `gh pr ready <number>`。提到 PR 状态之前，先运行 `origin pr view <number>` 或 `gh pr view <number>`。

**Babysit。** 开 PR 不等于开始 babysit。贴出 URL，继续构建。先把这个阶段或整个 stack 做完。只有整个 stack 都建好之后、用户又提出要求时，才单独跑一轮 babysit。每开一个新 PR 就 babysit 一次，会拖住构建，还会把检查花在后面几波推送会重跑的 commit 上。反馈偏离了原本的意图时，要提出异议。

开 PR 的子代理要跑 `interrogate`、`/deslop` 和 `/no-comments`，再贴出 URL。它回到父代理，不做 babysit，除非它是 Autopilot-full 或 Autopilot-stack 的负责人。这类负责人的任务说明里分配了 babysit 循环，这正是 `playbooks/babysit.md` 在等的那个请求。负责人在报告代码就绪之后开始这个循环，按它的 playbook 报告 merge-ready 或 STACK-READY，并且不受这里和 `playbooks/babysit.md` 里那些「整个 stack 建好之前不 babysit」的规则约束。
