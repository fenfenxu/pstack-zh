### Shipping

**合进去的东西由你负责。每个 PR 独立验证，只合入从根上开始、已经验证过的那一段，然后不要再碰这条 stack。**

1. **先确定用哪个代码托管平台，再在合入之前独立验证每个 PR。** 默认用 GitHub CLI（`gh`）。如果 `command -v origin` 成功，并且 Origin 能识别这个仓库，每一次 PR 操作都用 `origin pr`。否则继续用 `gh`，并记下这次回退。绝不要求 Graphite（`gt`）。每个 PR 一个验证者，每个都是一个 Cursor cloud agent，用对应的 control skill（例如 `cursor-team-kit` 的 `control-ui` 或 `control-cli`）按用户会做的方式跑这次改动，对照父分支和 head，并在这个 PR 上给出 `PASS`、`PASS+NOTES` 或 `FAIL`。只有没写这段代码的 agent 给出的 verdict 才算数，因为写这段代码的人核的是自己打算建成的东西。CI 变绿不是 verdict，机器人批准的评审也不是 verdict，因为两者都不会按用户的方式去跑这次改动。
2. **只合入从最底层开始、连续验证通过的那一段。** 从最底下还没合并的 PR 往上走，遇到第一个没有 `PASS` 或 `PASS+NOTES` 的就停。它下面的 PR 是这一段，最上面那个是上限。验证通过、但压在没验证的 PR 上面的那个要等，因为它离不开下面的代码就不能合入。自己的分支里既没有、也不需要那段代码的 PR，可以挪到 trunk 上自己合。它需要一份新的 verdict，因为旧的那份是在那段代码垫在下面时跑的，而 trunk 上没有那段代码。
3. **再确认一次，这些 verdict 说的仍然是这份代码。** 一份 verdict 覆盖一份补丁。整条链 rebase 会把上面每个 PR 挪到新的 SHA，却不会让任何检查失败。合入前，把 verdict 那个 SHA 上的 `git patch-id` 和当前 head 对比。两份补丁只在测试、文档或 lint 配置上不同时，如果某条 lane 跑过的东西在两个 SHA 上构建出来、除了噪声都一样，这条 lane 的结果就仍然有效。噪声是在 verdict 那个 SHA 上构建两次也会出现的差异，或者嵌进去的 commit SHA。每一种噪声都连同涉及的文件一起报告，好让读者能核对这个判断。改动本身仍要重新检查、重新评审，因为旧 verdict 没见过它。没有构建产物的 lane，例如开发服务器，证明不了这一点，所以要重跑。其余漂移了的，也要重新验证。
4. **从下往上，一次合一个 PR。** 始终合并这一段里最底下那个还没合并的 PR，用 `origin pr merge <pr>` 或 `gh pr merge <pr> --squash`。在 Origin 上，这条不带额外参数的命令还会合并你点名的那个 PR 下面的每一个 PR。在 GitHub 上，基础分支仍是父分支的 PR，会合进那个父分支，而不是 trunk。合并之后把 PR 再读一遍看结果（`origin pr view <pr>` 或 `gh pr view <pr> --json state,mergedAt,baseRefName`），因为合并命令可以在合并队列只是把 PR 排上队时就报告成功，请求了自动合并也不等于已经合并。合进去的 PR 读出来是已合并，并且基础分支是 trunk，不是父分支。只在 verdict 覆盖当前 head 的 PR 上设 `--auto`（Origin 的条件满足即合并，或 GitHub 的自动合并），因为它会把就绪的那个 head 合进去，不管是哪一个。一次合并落地之后，把基础分支曾是这条已合并分支的 PR 改到 trunk（`origin pr edit <pr> --base <trunk>` 或 `gh pr edit <pr> --base <trunk>`），把它的补丁挪到合并结果上，用 `--force-with-lease` 推送。verdict 覆盖的是补丁，不是其中的 commit，所以第 3 步的 patch-id 规则决定 verdict 还在不在。检查通过、并且 verdict 还在，就合并它。
5. **stack 在合入期间不要去动它。** 对分支或基础分支的改动，只有第 4 步挪下一个 PR 这一次。合并会把当时分支和基础分支上的东西合进去，不管有没有 verdict。在 GitHub 上，用 `playbooks/babysit.md` 里的排队监视（`scripts/watch-pr/watch-pr --queued-stack --stack-prs <bottom-to-top>`），放在 `/loop` 下，直到它在上限处报告 `COMPLETE`。在 Origin 上，每次 `origin pr checks <pr> --watch` 返回之后，用 `origin pr view <pr> --checks` 再读一遍当前 PR，直到上限读出来是已合并。父 PR 合并时基础分支挪到 trunk，是预期的，不是要去修的损坏。合并卡住时，先弄清原因，再改任何东西。
6. **到上限就停。** 把这一段往上延需要新的 verdict，所以要从第 1 步重新走一遍。

**回复：** 验证通过的那一段和它的上限、每个 PR 的 verdict 以及是谁给的、合进去了什么以及你怎么确认的，以及下一个缺口需要什么。
