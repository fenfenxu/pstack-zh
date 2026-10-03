### Autopilot-stack

**你负责整条 PR 栈，但绝不落地合并。** 在完全自主下构建并验证队列，然后交给操作者一条可线性审查并合并的基线分支栈。与 **Autopilot-full** 互为姊妹 playbook。

1. **owner 循环保持不变。** 为整个程序一次性解析 forge。GitHub CLI（`gh`）为默认。若 `command -v origin` 成功且 Origin 能解析该仓库，则 PR 的创建、编辑、查看、监听与合并使用 `origin pr ...`；否则继续使用 `gh`，并记录回退方案。绝不强制要求 Graphite（`gt`）。每个 PR 由一名 Cursor 云 agent 端到端负责：构建、首次推送、在自证前打开就绪 PR、自证（门禁、CI、回执）、按 `../references/bugbot-triage.md` 做审慎 Bugbot 分诊、slop-strip（`cursor-team-kit` 插件中的 `deslop` skill（`/deslop`））、`/no-comments`（**no-comments** skill），以及按 `playbooks/babysit.md` 看护至全绿。工作彼此独立时，各 owner 可并行。每个 owner 应在约 15 分钟内：按 **show-me-your-work** skill 启动 `decisions.tsv` 轨迹、推送首个分支快照，并以就绪状态（非 draft）打开 PR。此后每完成一个可验证单元再推送一次分支（检查钩子保持开启，未完成的提交也可以）。轨迹保持未提交，并在报告中一并返回。owner 还需维护 Autopilot-full 第 2 步中的 `children.tsv`。
2. **按真实循环巡检。** 根节点每小时运行一次审计 tick。操作者 go 时，根节点用一段执行本次 tick 的提示配置 `/loop 1h`，按 Autopilot-full 第 6 步。绝不要把节奏交给记忆或易丢失的完成通知。每次 tick：用 `git show origin/main:pstack/skills/poteto-mode/playbooks/autopilot-stack.md` 从 trunk 重读本 playbook，并对照它审计当前操作，在该 tick 内修正漂移。对每个 owner 做通用的存活或状态探测。仅将副作用计为进展：提交、推送、PR 或检查项变化、以及 store 报告。若某 lane 超过预期运行时间仍无副作用，视为卡住；立即令其停手并派替补，不要等待礼貌性返回。按 Autopilot-full 第 6 步探测所有 subagent 并结束 tick。
3. **守住操作者门禁。** 先陈述再等待：请求陈述计划不等于放行。操作者 stop 时，所有 owner 立即进入零写入冻结。
4. **逐轮验证。** 代码定稿后，owner 报告 code-ready 的 head SHA；循环全绿后报告 STACK-READY 及精确 head SHA。根节点按 Autopilot-full 第 4 步验证每一轮，但以 STACK-READY 替代 merge-ready。未经验证的内容不得入栈。
5. **干净裁决后追加，绝不自行发布。** 任何 owner 不得合并、启用 auto-merge 或关闭 PR。干净裁决将 PR 按已验证顺序（或操作者指定顺序）追加到唯一一条线性基线分支栈。
6. **拓扑单写者，构建并行写者。** owner 只推送自己的分支，并报告 tip、当前 base 与预期 parent。根节点是唯一拓扑写者。追加 PR 时：fetch 预期 parent，将子分支 rebase 到该 parent 的精确 tip，在 `ls-remote` 检查通过后仅用 `--force-with-lease` 推送，并将 PR base 设为 parent 分支。按已解析 forge 用 `origin pr create --status open --base <parent-branch>` 或 `gh pr create --base <parent-branch>` 创建；已有 PR 用 `origin pr edit <pr> --base <parent-branch>` 或 `gh pr edit <pr> --base <parent-branch>` 改 base。仅根 PR 以 trunk 为目标。绝不通过 `gt` 提交或注册整条链。
7. **在根节点吸收漂移，再重验已移动部分。** 根节点 fetch 当前 trunk，自底向上 rebase 整条链。rebase 在 owner 文件上产生冲突时，由该 owner 修复其切片，根节点推送结果。rebase 会重写其上方所有 SHA，并使旧 SHA 上的裁决失效。在每个裁决 SHA 上应用 `playbooks/shipping.md` 中的 patch-id 规则。仍无效的内容须回到本 playbook 第 4 步后再交付。每次重写推送后，即使 patch-id 未变，也要重跑 mergeability 与 CI。会签规则与 Autopilot-full 相同。真正新的、需要固定下来的门禁值，要根节点重新会签；吸收已落地值的漂移不算 raise。
8. **交付整条链。** 交付物是一条线性、已验证 PR 链，可在已解析 forge 中自底向上审查；每个链接在 PR 正文或评论中携带验证者裁决。操作者自行审查并落地，可手动点击，也可启用 merge-when-ready。

**如何在两种 autopilot 间选择。** PR 彼此独立且授予落地权限时用 Autopilot-full。操作者希望先审后合、工作有序或耦合、或未授予合并权限时用 Autopilot-stack。

**回复内容：** 栈根与栈顶的链接、每个链接一行裁决摘要、以及因何原因搁置或排除的项。
