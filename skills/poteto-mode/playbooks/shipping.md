### Shipping

**你负责落地内容。独立验证每个 PR，仅从 root 落地已验证的连续段，之后不要动队列。**

这是 `playbooks/babysit.md` 之后的半程。

1. **先解析 forge，再独立验证每个 PR。** GitHub CLI（`gh`）为默认。若 `command -v origin` 成功且 Origin 能解析仓库，view、watch、edit、merge 等 PR 操作用 `origin pr ...`。否则继续用 `gh` 并记录回退。不要要求 Graphite（`gt`）。每个 PR 一个子代理，不批量；各为 Cursor cloud agent，用匹配的 control skill（如 `cursor-team-kit` 的 `control-ui` 或 `control-cli`）对 parent 与 head 驱动真实界面。各返回 `PASS`、`PASS+NOTES` 或 `FAIL`，并在各自 PR 上发布该裁决。Safe 指未编写该代码的 agent 给出的裁决。CI 全绿不是裁决，bot 批准 review 也不是裁决。
2. **仅落地自底向上、root 处的连续已验证段。** 从最低未合并 PR 向上走，在第一个无通过裁决处停止；`PASS` 与 `PASS+NOTES` 均算通过。已验证 PR 若位于未验证 PR 之上则不可落地。将天花板报告为 PR 编号并说明链何处断裂。
3. **确认每个裁决仍描述当前 patch。** 记录裁决 head SHA、base SHA，以及该 PR base-to-head diff 的稳定 `git patch-id`。rebase 或 base retarget 会改写 SHA，可能在不动 check 的情况下静默使裁决失效。落地 PR 前，将记录的 patch-id 与当前 base-to-head patch-id 比较。当两份 patch 仅在 tests、docs 或 lint 配置上不同时，构建各 lane 所跑内容：在裁决 SHA 上构建两次，在当前 head 上构建一次。若两次裁决 SHA 构建也呈现该差异，或差异为嵌入的 commit SHA，则为噪声。按差异种类而非按文件判断，并报告各类噪声及其文件。若仅噪声不同，该 lane 结果仍有效，checks 与变更 review 须重新运行。不要复用 dev server 或无构建输出的 lane 结果，须重跑该 lane。patch 变更时重新验证其余项；未变更时保留代码裁决，但在当前 head 重跑 mergeability 与 CI。不要用相同 commit message 或旧 SHA 的绿色 check 替代。
4. **仅准备最底 PR。** 拉取当前 trunk。必要时将最低已验证分支 rebase 到 trunk 精确 tip，push，并仅用 `origin pr edit <pr> --base <trunk>` 或 `gh pr edit <pr> --base <trunk>` 将该 PR retarget 到 trunk。push 后重跑第 3 步。尚未 retarget、arm 或合并后代。
5. **一次合并一个 PR。** 若最底 PR 现已可合并，用 `origin pr merge <pr> --squash` 或 `gh pr merge <pr> --squash` squash 合并。若 requirements 仍在跑且用户要求 merge-when-ready，仅用 `origin pr merge <pr> --squash --auto` 或 `gh pr merge <pr> --squash --auto` arm 该 PR。Origin 的 `--auto` 为 Origin merge-when-ready。GitHub 的 `--auto` 为 GitHub auto-merge。等待该 PR 合并后再准备下一个。
6. **不要将 GitHub `autoMergeRequest` 读作栈就绪。** 它最多表示已为单个 GitHub PR 请求 auto-merge。不能证明 Origin merge-when-ready 已 arm、后代已入队、patch 裁决仍有效，或连续栈安全。确认当前最底 PR 在活跃 forge 上的状态；若活跃 forge 无法报告，说明状态未知。
7. **每次合并后重新计算。** 拉取 trunk，确认已合并 SHA 存在，从冻结的自底向上列表去掉已合并 PR，检查新最底 PR 的 base、head、checks 与 patch-id。宿主可能自动 retarget 子 PR，但不要假设已发生。对该 PR 重复第 3 至 6 步。独立工作不在此链内，自行 ship。
8. **监视当前前沿直至合并或失败。不要在其周围变更队列。** Origin 上用 `origin pr view <pr> --checks --comments` 与 `origin pr checks <pr> --watch`，然后重读 PR 直至报告 merged 或 blocked。GitHub 上用 `scripts/watch-pr/watch-pr --queued-stack --stack-prs <bottom>` 仅作事件唤醒，每次唤醒后轮询 `gh pr view <pr> --json state,mergedAt,mergeStateStatus,statusCheckRollup,autoMergeRequest`，在 `mergedAt` 非空或 `state` 为 `MERGED` 之前忽略 `READY`。然后才执行第 7 步。仅当 `state` 为 `CLOSED` 且无 `mergedAt`、必需 check 以 `FAILURE` 或 `CANCELLED` 结束且在 auto-merge 不再 pending 时阻塞合并，或 `mergeStateStatus` 为 `UNSTABLE` 或 `DIRTY` 且无 auto-merge pending 时硬失败。checks pending 或 auto-merge 已 arm 时的 `BLOCKED` 不是失败。此处不要用 Babysit 的 queued `WAITING`/`merge-queue` 停止条件。在 dynamic 模式下于 `/loop` 中保持 watch。报告每次合并与新天花板。队列停滞时先诊断再变更。
9. **在天花板处停止。** 已验证段合并后，报告已落地内容、下一个未验证 PR 是什么，以及验证它需要什么。扩展该段需重新走第 1 步。

**回复：** 已验证段及其天花板、各 PR 裁决及产出者、已 arm 内容及确认方式、已落地内容，以及下一缺口所需事项。
