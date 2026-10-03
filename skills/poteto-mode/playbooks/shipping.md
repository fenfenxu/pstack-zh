### Shipping

**合进去的东西由你负责。每个 PR 独立验证，只合入从最底层开始、连续验证通过的那一段，然后不要再碰队列。**

这是接在 `playbooks/babysit.md` 后面的另一半。

1. **先确定用哪个代码托管平台，再独立验证每个 PR。** 默认用 GitHub CLI（`gh`）。如果 `command -v origin` 成功、而且 Origin 能识别这个仓库，查看、监视、编辑和合并 PR 都用 `origin pr ...`。否则继续用 `gh`，并记下这次改用了备用方案。绝不要求用 Graphite（`gt`）。每个 PR 一个子代理，不要合并成一批，每个子代理都是一个 Cursor cloud agent，都用对应的 control skill（例如 `cursor-team-kit` 里的 `control-ui` 或 `control-cli`）在真实界面上把父分支和 head 各操作一遍。每个子代理返回 `PASS`、`PASS+NOTES` 或 `FAIL`，并把这个 verdict（结论）发在它自己的 PR 上。安全指的是一个没写这段代码的 agent 给出的 verdict。CI 变绿不是 verdict，机器人批准的评审也不是 verdict。
2. **只合入从最底层开始、连续验证通过的那一段。** 从最底下还没合并的 PR 往上走，遇到第一个没有通过 verdict 的就停，`PASS` 和 `PASS+NOTES` 都算通过。一个验证通过的 PR，如果下面压着一个没验证的，就不能合。用 PR 编号报告这一段的上限，并说明是什么打断了这条链。
3. **再确认一次，每个 verdict 说的仍然是这份补丁。** 记下 verdict 对应的 head SHA、base SHA，以及这个 PR 从 base 到 head 的 diff 的稳定 `git patch-id`。rebase 或改目标分支会改写 SHA，可能在不动任何检查的情况下，悄悄让一个 verdict 失效。合入一个 PR 之前，把记下的 patch-id 和它当前从 base 到 head 的 patch-id 对比。如果两份补丁只在测试、文档或 lint 配置上不同，就把每条 lane（并行验证的一条线）跑过的东西构建出来：在 verdict 的 SHA 上构建两次，在当前 head 上构建一次。如果在 verdict 的 SHA 上构建的两次也出现同样的差异，或者差异只是嵌进去的 commit SHA，那就是噪声。按每一处差异判断，而不是按每个文件，并按噪声的种类分别报告涉及哪些文件。如果只有噪声不同，这条 lane 的结果仍然有效，但检查和对这次改动的评审要重新跑。来自开发服务器、或者其他任何没有构建产物的 lane 结果，不能沿用，那条 lane 要重跑。补丁变了，其余的都要重新验证。补丁没变，代码的 verdict 保留，但要在当前 head 上重跑能否合并的检查和 CI。绝不用 commit 信息相同、或者旧 SHA 上的绿色检查来顶替。
4. **只准备最底下那个 PR。** 拉取最新的 trunk。需要的话，把最底下那个验证通过的分支 rebase 到 trunk 当前的最新提交上，推送，然后只把这一个 PR 的目标分支改成 trunk，用 `origin pr edit <pr> --base <trunk>` 或 `gh pr edit <pr> --base <trunk>`。推送之后重跑第 3 步。上面的 PR 先不要改目标、不要设自动合并、不要合并。
5. **一次合一个 PR。** 如果最底下那个 PR 现在就能合，用 `origin pr merge <pr> --squash` 或 `gh pr merge <pr> --squash` 压缩合并。如果合并要求还在跑、而用户要求条件满足就合并，只给这一个 PR 设上自动合并，用 `origin pr merge <pr> --squash --auto` 或 `gh pr merge <pr> --squash --auto`。Origin 的 `--auto` 是 Origin 的条件满足即合并。GitHub 的 `--auto` 是 GitHub 的自动合并。等这个 PR 合进去，再准备下一个。
6. **不要把 GitHub 的 `autoMergeRequest` 当成整个 stack 已经就绪。** 它最多只说明某一个 GitHub PR 请求了 GitHub 的自动合并。它证明不了 Origin 的条件满足即合并已经设上，证明不了上面的 PR 已经排进队列，证明不了补丁的 verdict 仍然有效，也证明不了这段连续的 stack 是安全的。去当前所用的托管平台确认最底下那个 PR 的状态。如果平台报告不了，就说状态未知。
7. **每合一个就重新算一遍。** 拉取 trunk，确认合进去的 SHA 在里面，把合并了的 PR 从冻结好的从下到上的列表里去掉，再检查新的最底层 PR 的 base、head、检查状态和 patch-id。托管平台可能会自动把子 PR 的目标改掉，但不要想当然地认为它改了。对这一个 PR 重复第 3 到第 6 步。互相独立的工作不在这条链里，各自单独交付。
8. **盯着当前的 frontier（最前沿那个 PR），直到它合并或失败。不要在它周围改动队列。** 用 Origin 时，运行 `origin pr view <pr> --checks --comments` 和 `origin pr checks <pr> --watch`，然后反复读这个 PR，直到它显示已合并或被阻塞。用 GitHub 时，`scripts/watch-pr/watch-pr --queued-stack --stack-prs <bottom>` 只用来在有事件时唤醒你，每次唤醒后轮询 `gh pr view <pr> --json state,mergedAt,mergeStateStatus,statusCheckRollup,autoMergeRequest`，在 `mergedAt` 不为空或 `state` 为 `MERGED` 之前，忽略 `READY`。到这时才跑第 7 步。只有下面几种情况才算硬失败：`state` 是 `CLOSED` 且没有 `mergedAt`；某个必需的检查以 `FAILURE` 或 `CANCELLED` 结束，并在自动合并不再等待之后挡住了合并；或者 `mergeStateStatus` 是 `UNSTABLE` 或 `DIRTY` 且没有在等待的自动合并。检查还在跑、或自动合并已经设上时出现的 `BLOCKED`，不算失败。这里不要用 Babysit 里排队时的 `WAITING`/`merge-queue` 停止条件。用 `/loop` 的动态模式持续盯着。每合并一个，就报告一次，连同新的上限。如果队列卡住了，先诊断，再改动。
9. **到上限就停。** 验证通过的那一段全部合并后，报告合进去了什么、下一个没验证的 PR 是哪个，以及验证它需要做什么。要把这一段往上延，就从第 1 步重新走一遍。

**回复：** 验证通过的那一段和它的上限、每个 PR 的 verdict 以及是谁给的、你设了哪些自动合并、怎么确认的、合进去了什么，以及下一个缺口需要什么。
