### Autopilot-stack

**你负责 stack，合入从来不归你。你完全自主地构建并验证整条队列，然后交给操作者一条从基础分支往上叠的线性 stack（每个 PR 都以前一个为基础），由操作者评审并合入。** 它和 **Autopilot-full** 是姊妹 playbook。

1. **照原样跑负责人循环。** 整个项目只确定一次代码托管平台。默认用 GitHub CLI（`gh`）。如果 `command -v origin` 成功，并且 Origin 能解析这个仓库，PR 的创建、编辑、查看、监视和合并都用 `origin pr ...`。否则继续用 `gh`，并记下这次回退。绝不把 Graphite（`gt`）当成必需。每个 PR 由一个 Cursor cloud agent 从头到尾负责自己的改动：构建、第一次推送、在自证之前先开出就绪的 PR、自证（门禁、CI、回执）、按 `../references/bugbot-triage.md` 带着怀疑分诊 Bugbot 的评论、去 slop（清掉 AI 腔的空话套话，用 `cursor-team-kit` 插件里的 `deslop` skill，即 `/deslop`）、`/no-comments`（**no-comments** skill），以及按 `playbooks/babysit.md` 看护到全绿。工作自成一体时，各负责人并行推进。每个负责人在大约 15 分钟内，按 **show-me-your-work** skill 开始记 `decisions.tsv` trail（决策记录），推送第一个分支快照，并以就绪状态开 PR，绝不开草稿。此后每完成一个可验证的单元，负责人就再推送一次分支（hook 保持开启，提交半成品也可以）。trail 不要提交，放在报告里交回。负责人还要维护 Autopilot-full 第 2 步说的 `children.tsv`。
2. **靠真实的定时循环审计。** 根代理每小时做一次审计巡检。操作者下令开始后，根代理按 Autopilot-full 第 6 步，用一段执行这次巡检的提示词设好 `/loop 1h`。巡检的节奏绝不能交给记忆，也不能交给会丢失的完成通知。每次巡检，用 `git show origin/main:pstack/skills/poteto-mode/playbooks/autopilot-stack.md` 从 trunk（主干分支）重读这份 playbook，并对照它审计整个运作。发现偏离，就在这次巡检里修正。用通用的存活或状态检查探测每个负责人。只把副作用算作进展：提交、推送、PR 或检查项的变化，以及存储目录里的报告。某条 lane（一路独立推进的工作）超过预期运行时间还没有任何副作用，就当它卡住了。立刻让它停手，马上派替补。不要等它体面地自己回来。探测所有子代理、结束这次巡检，都按 Autopilot-full 第 6 步来做。
3. **守住操作者的门禁。** 先说明，再等待。所以操作者让你说明计划，并不等于让你开始。操作者喊停时，每个负责人立刻原地暂停，不再做任何写入。
4. **每一轮都验证。** 要交付的代码定稿后，负责人报告 code-ready（代码就绪）的 head SHA。它的循环全绿后，再报告 STACK-READY（可以进 stack），并附上确切的 head SHA。根代理按 Autopilot-full 第 4 步验证每一轮，只是用 STACK-READY 代替那里的 merge-ready（可以合并）。没有验证过的东西，一律不进 stack。
5. **拿到干净的 verdict（验证结论）再追加，绝不发布。** 负责人一律不合并、不开启自动合并、不关闭 PR。verdict 干净，就把这个 PR 追加到那唯一一条从基础分支往上叠的线性 stack 上，顺序按验证通过的先后，或者按操作者指定的顺序。
6. **stack 拓扑只有一个写者，构建可以多个写者并行。** 负责人只推送自己的分支，并报告分支的最新提交、当前的基础分支和预定的父分支。根代理是拓扑唯一的写者。追加一个 PR 时，先 fetch 预定的父分支，把子分支 rebase 到这个父分支确切的最新提交上。只有在 `ls-remote` 检查之后，才用 `--force-with-lease` 推送。然后把 PR 的基础分支设成父分支。按确定下来的托管平台，用 `origin pr create --status open --base <parent-branch>` 或 `gh pr create --base <parent-branch>` 创建 PR。已有的 PR，用 `origin pr edit <pr> --base <parent-branch>` 或 `gh pr edit <pr> --base <parent-branch>` 改目标分支。只有根 PR（stack 最底下那个）以 trunk 为目标分支。绝不用 `gt` submit 这条链，也不用它登记这条链。
7. **由根代理吸收漂移，再重新验证变动过的部分。** 根代理 fetch 当前的 trunk，从下往上依次 rebase 整条链。rebase 在某个负责人的文件里冒出冲突时，由这个负责人修自己那一段，根代理推送结果。一次 rebase 会改写它上方所有的 SHA，旧 SHA 上的 verdict 随之作废。在每个有 verdict 的 SHA 上，套用 `playbooks/shipping.md` 里的 patch-id 规则。不再有效的，交付之前都要回到本 playbook 第 4 步重走一遍。每次推送了改写过的提交，都要重跑可合并性检查和 CI，patch-id 没变也一样。会签规则与 Autopilot-full 相同。真正新增的锁定值会让流程停下，等根代理重新会签。吸收已合入数值的漂移，不算上调锁定值。
8. **交付整条链。** 交付物是一条由已验证 PR 组成的线性链，可以在确定下来的托管平台上从下往上逐个评审。链上每一环都在 PR 正文或评论里附上验证者给出的 verdict。由操作者评审并合入，可以自己点按钮，也可以开启 merge-when-ready（就绪后自动合并）。

**两种 autopilot 怎么选。** PR 彼此独立，而且操作者授予了合入权限时，用 Autopilot-full。操作者想在合入前先评审、工作有先后顺序或互相耦合，或者操作者不授予合并权限时，用 Autopilot-stack。

**回复：** stack 根部和顶端的链接，每一环一行 verdict 摘要，以及搁置或排除的项和原因。
