### Orchestrate

**你负责整个程序，但不写代码。** 撰写 brief、清空队列、保持 frontier 全绿、做判断。适用于交给一个常设协调者会话的整项工程：跨多日、大量 stacked PR、数十至数百 subagent，人类一天查两次而非每五分钟盯一次。单任务推到可判定谓词是 Autonomous run；需要定制工作流的 ambitious run 是 figure-it-out。工作超出任一 agent 生命周期时路由到此。单 agent 能在会话预算内完成的不算 program。

仪式须随 program 规模伸缩。在廉价且近乎相同的 unit 上，按各节指示压缩。

三条规则承载其余一切。

- 完成是队列事件，不是中断。
- 每次 spawn 与每次 resume 都原样携带 standing orders。
- brief 即产品。模糊的 brief 会静悄悄地失败，因为 worker 无法向你提问。

#### 角色与部署

- **协调者（本会话）。** 本地。定框、写 brief、清空 inbox、拥有人类报告、做判断。从不撰写或编辑代码。冲突合并、restack 与代码变更始终是 task。对已验证 unit 的机械落地（worker 提交的 fast-forward 或干净 cherry-pick 后 push）在本地 git 廉价的仓库上可由协调者自行做 bookkeeping。把已完成工作排在空闲 stacker 后面会让 deadline 颗粒无收。循环端到端 agentic：仅通过 Task tool 启动、恢复、清空 agent。状态读写仅在 drain 点经 `scripts/orch/orch.ts`，一条命令进、一行出。CLI 从不启动、等待或唤醒任何对象。
- **子协调者。** 始终本地、持久、每 track 一个，且仅当 program 超出单个协调者 drain 能力时启用。协调者能自行 drain 的 track 不需要中间层。每嵌一层都要重付完整 orientation preamble；阻塞型子协调者会隐藏其 children，使 parent 空转。拥有该 track 的 unit 与 board，撰写 worker brief，启动自己的 worker 与 verifier（嵌套深度可达 3，嵌套 spawn 拥有完整 Task schema，含 `environment`）。在 wave 边界 rollup 聚合。从不转发原始 child 报告。在途 children 上限为单次 drain 可处理量，约十个，滚动窗口；绝不以阻塞 batch 形式，那会付出每批最慢 child 的代价。
- **Worker / verifier。** 除非任务需要本机，否则始终 `environment: "cloud"`：`cursor-team-kit` 的 `control-ui` 或 `control-cli` 运行时验证；读取 `agent-transcripts/` 下本地 transcript；模拟器与本地 IDE 状态；仅本机存在的 auth。云 agent 无法读本地 store，brief 须内联所需信息或指向 repo 路径。倾向更少、更宽的 worker。每个 worktree 或分支单写者（principle-separate-before-serializing-shared-state）。unit 的 verifier 使用与 worker 不同的 model family。

深度止于协调者、track、worker。按项目撰写 track 分解（build、landing、verification 是常见切分，非固定形状）。硬编码 swarm 树已试过并搁置，过于僵化。

#### Store 布局

在当前 agent 的 store（系统提示中的路径）创建 `orchestrate/<project-slug>/`。每个文件恰好一个写者。owner 发布事实，读者在读取时聚合。簿记用 `bun scripts/orch/orch.ts`，下文简称 `orch`；其规范 plain TSV 与 JSON 无 CLI 也可读。

- `preferences.md` 是 standing-orders 寄存器：编号行，每行一条约束（model policy、stack 形状与数量、验证门槛、禁止路径、升级策略）。每次 spawn 与 resume 原样粘贴。指令在 resume 间会衰减，每丢一条都耗费人类一轮。若发现自己重复某条指令，行动前先追加该行（principle-encode-lessons-in-structure）。
- `overview.md` 是持久的 PR 与 issue DB。追加写入；绝不因单个事件整篇重写。
- `units.tsv` 每 unit 一行：id、track、state、branch、PR、head SHA、brief path。行内更新。
- `frontier.json` 是计算出的 merge frontier，见 Stack safety。
- `ledger.tsv` 是验证账本，见 Verification。
- `inbox/` 存放完成指针。`gates.md` 停放人类门禁（问题、选项、无答复时的默认）。
- `decisions.tsv` 是经 show-me-your-work skill 的轨迹。
- `status.md` 由每次 drain 时从 `units.tsv` 与 `ledger.tsv` 派生，从不手维护；从表重新生成，勿把事件叙述写进其中。

#### Brief

你给 agent 的 prompt 是唯一产品；草率的 brief 会在整棵树中层层放大为劣质产出。每次 spawn 携带全部内容。填不出的字段说明 unit 尚未界定范围。

```
GOAL         one sentence, the outcome, executable by a stranger with no chat access
SCOPE        paths this unit may write; paths it may not; its exclusive worktree or branch
CONTEXT      pointers to files and PRs; upstream reports pasted in full when this unit
             depends on them, because workers cannot see siblings
ACCEPTANCE   checkable criteria, one per line
VERIFY       exact commands or the control-skill path, plus known gotchas
TIMEBOX      rough cap on runtime; on expiry, return partial findings and stop rather than run on
FORBIDDEN    no gt, no rebase, no force-push, no fixes outside scope, plus unit-specific bans
REPORT       status, branch, head SHA, PRs, verdict, what you actually ran, deviations,
             suggested follow-ups
STANDING     <preferences.md pasted verbatim>
```

brief 按 unit 规模调整。单命令 unit 可将模板压成一段，仍须写明 goal、scope、verify 命令与 report 形状。两行 edit 外包 4KB scaffold，读写遵循的成本高于 edit 本身。本地 spawn 可引用 store 路径下的 standing-orders 文件；云 spawn 与每次 resume 须原样粘贴。

子协调者 brief 另加：track 边界与 unit 列表、spawn budget（云默认与本地例外列表）、drain 协议、rollup 格式（每 child：name、status、PR、head SHA、verdict、一行摘要；外加 track status 与 frontier delta）。

依赖是上下文传递，不只是顺序。前面步骤里有上下文却没写进 brief，worker 就会猜。缺字段即拒绝启动的条件。每个 sub-coordinator 每 wave 抽样审计一条 worker brief，与该 wave 并发进行，绝不挡在该 wave 前面。brief 失败则停该 track，修正子协调者指令而不只修 worker，因为 brief 质量在 run 后期会衰减。绝不通过 resume 链式传递 brief；合并 scope 后重新启动。

#### 步骤

1. **定框（Frame）。** 将 done 谓词表述为可计数（如「126 个 unit 全部 merged，每条 ledger 验证为 `unit-test-verified` 或更好」）。量化 scope：unit 数、粗 effort、预期 stack、墙钟 budget。若单 agent 能在该 budget 内完成，在此停止并改跑 Autonomous run。压缩不依赖另一份文档存在；指在本会话直接干活：plain worker 按需、验证 inline、边做边落地，不用下文 store、register 或 pilot  machinery。按 budget 安排 landing：约 70% 时停止 spawn，落地已验证部分。按项目命名 track。有争议的分解或 one-way door 在 pilot 前走 arena skill。定框只呈现一次；可逆准备不必等待。
2. **安装运行时。** 运行 `orch init`。经 show-me-your-work skill 打开 trail；任何 spawn 前先写 standing orders；用 `orch frontier set --repo <repo-dir>` 从现有 PR seed `frontier.json`。
3. **Pilot。** 推一个 unit 走完整路径：brief、worker、verification、stack 入栈、ledger 行、merge。pilot 用于在只花一个 agent 而非五十个时 falsify brief 模板、verify recipe 与 unit 大小。fan-out 前用 pilot 证据修正 contract。pilot 规模随 unit。近乎相同廉价 unit 的大 program 中，第一个 unit 即 pilot，作为普通 unit 内联 verify 命令，落地后立即 fan-out。专用 pilot 流水线（独立 verifier agent、audit gate）用于昂贵或新颖 unit 形状，不用于 clone-unit（串行 pilot 无可 falsify）。
4. **Scale。** spawn 滚动窗口 worker 直至在途上限，child 完成即 refill。阻塞 batch 付出每批最慢 child 代价。仅超过 Roles 中单 drain 阈值时 spawn track 子协调者。每次 drain 后重算 ready work。把前面步骤的报告写进后面步骤的 brief。兄弟通信仅向上。抽样 brief 审计与所抽 wave 并行；失败时停下一 refill，不停当前 wave。
5. **Drain。** 每个 drain 点运行下文队列纪律。
6. **Land。** Landing 持续进行，不是终局阶段。从第一个 verified unit 起 integration 与剩余 wave 并行。重 repo 上 stacker 从 wave one 起为常设角色，unit verify 即 integrate。本地 git 廉价的 repo 上协调者按 Roles 自行落地 verified unit。upper-stack 工作前保持 frontier 全绿。受 Stack safety 约束。仅在 merge 或报告新 head SHA 时推进 `frontier.json`。
7. **Close。** 清空最终 inbox；每个 spawned agent 对账到终态行（done、abandoned、zombie-reconciled）；在真实产物上确认谓词；每个 landed PR 对其当前 head SHA 有 verdict；按 show-me-your-work 审计 trail（含 cross-model review）；将 recurring correction 写入 `preferences.md` 或 brief 模板。store 保持完整，供 postmortem 使用。

#### 队列与 drain

- 收到完成通知时运行 `orch inbox push <agent> <unit> <status> [--report PATH]`，然后回到原工作。绝不在 drain 内做深度审查。需 review 的 completion 变为 verifier unit。绝不在 drain 内 review diff。
- 在四个点 batch drain：关键段结束、track rollup、frontier watcher 唤醒（经 loop skill 配置，长 heartbeat 回退）、人类报告前。每批以 `orch inbox drain` 开始；drain 期间到达的等下一批。
- 须先完成的关键段：写 brief、stack 操作、冲突决策、写 gate、更新 ledger 或 frontier。
- 每批 drain 对每个指针分类（landed、needs-verify、failed、zombie、noise），经 `orch unit add`、`orch unit set`、`orch ledger record` 写行，运行 `orch status`，再于一条消息中 spawn 下一 wave。
- 在 track rollup 对每个 spawned child 入账：已到达、已 respawn，或 scope 已明确 absorb。静默重做缺失 child 的工作会同时隐藏浪费与 coverage gap。
- drain 轮次以 `orch status` 三行结束：各 state 计数、变化摘要、开放 gate。细节在 `status.md`。checkpoint 与 close 适用完整 reply contract。

#### Stack safety

- frontier 是计算对象，不是叙述。每次 merge 与 stack 变更后用 `gt` 重算 `frontier.json`，因 GitHub base ref 在 restack 中会漂移而 gt tracking 为权威：有序 PR 列表、分支名、head SHA、generation 号、最低未 merge PR。在 gt 知晓 stack 处解析，通常是 stacker clone。checkout 的 gt metadata 从未见过 submit 则报无 PR，命令 error 而非猜测。
- 每个 stack 恰好一名 stacker 可运行 `gt`，stack 内串行。holder 记入 standing orders。restack 在 cloud 运行；此规模本地 restack 会拖垮笔记本。
- worker 从不 rebase、从不运行 `gt`。babysitter 按 `playbooks/babysit.md`，每 stack 一个，scope 到不可变 frontier generation。冲突报告 stacker，而非自行 restack。
- PR close 与 retarget 仅经 stacker。close base PR 会使其上整条链 orphan。merge 与 stack surgery 是带 brief 的 unit，与其他 unit 相同。
- 一名 retro watcher 跟踪 merged PR 的 revert、post-merge CI  breakage 与 orphan follow-up。

#### Verification

验证随 unit 规模伸缩。VERIFY 为单条廉价命令时，worker 运行并报告输出，协调者抽查回执。专用 verifier agent（与 worker 不同 model family）用于验证昂贵、需判断或高 blast-radius 的 unit。整个产物只是重跑一条命令的 verifier agent 是 ceremony，不是 verification。

用 `orch ledger record` 写 ledger 行。用 `orch ledger check` 查当前 PR 与 head SHA。`ledger.tsv` 每 verdict 一行，键为 PR number 加 head SHA：`live-ui-verified | unit-test-verified | type-check-only | verifier-blocked | verifier-failed`。CI 绿是 verdict 输入，不是 verdict。行为性工作需优于 `type-check-only`。`verifier-blocked` 不是 pass。环境恢复后 respawn。`verifier-failed` 开 fix unit，不是 re-verify。worker 可 self-report；verifier 在同键上覆盖。新 head SHA 使行作废，restack 后重新验证。ledger 回答「是否 verified」，不靠记忆或 transcript。

unit 在 output 落地瞬间即外化，绝不攒到运行结束。worker push 分支，verifier 写 ledger 行，回执进 store。仅存在于某 VM 的工作在该 VM 死亡时等于从未完成。

#### 存活与失败

- 绝不 resume agent 来查进度。resume 会重启 idle agent。只读探测：ledger、`units.tsv`、`gh`、已 push 分支、Cursor dashboard 中云 agent status。transcript mtime 不是 liveness。
- 静默死亡在 inbox 写 synthetic postmortem 行（unit、failure mode、最后证据、选项）。证据到达即 replan。绝不等待完全静默。
- 按模式重试：cap-hit 或 oom，缩小 scope respawn；network-drop，原样 retry；tool-error，换 model retry；unknown，retry 一次。两次 retry 后 abandon unit 并围绕 replan。
- 迟数小时返回的 zombie 在接受任何东西前对照当前 frontier 与 ledger reconcile。独特 finding 经 fresh unit salvage，绝不盲目合并。
- 继续 spawn 会在整树产出垃圾（前面步骤的坏结果、broken acceptance、dead infra）时，在 standing orders 顶部写 stop line，让在途工作结束，修原因，再清除。
- 对自身 infra retry 与对 child 同样设界。连续数次 tool abort 后停止 retry；写 terminal handoff 到持久 state（已完成项、所在位置、精确 resume 命令）并结束 run。
- Cursor 重启后：本地 agent 已死，云工作仍在。重读 standing orders 与 `units.tsv`，重算 frontier，按 PR 与 branch 而非 agent id 重新 attach 云工作，从 stored brief 加当前 state 每 track respawn 一名 sub-coordinator，drain，resume。死 session 的 store lock 在下次 write 时自行清除。holder pid 已无时 `orch` 替换 lock。

#### 升级

触达人类，batch 进 status page 而非逐项：不可逆操作（对共享分支 force-push、deploy、删除、close 他人 PR）；无实验能定的 genuine 产品或偏好决策；standing order 与观测现实矛盾；replan 后仍存在的 program 级 dead end。每项在提问前作为 `gates.md` 条目搁置，并绕开路由工作。

不触达人类：frontier nudge、restack  mechanics、retry、CI flake 分诊、review thread 分诊、format fix、brief 已禁止的 scope（拒绝并继续）、以及「是否继续」。有疑则行动并记录。

run 中发现仅修阻塞 frontier 的部分；其余搁置到 follow-up。在此 fan-out 下小 scope leak 会乘成无人要的 PR。

**回复内容：** checkpoint 与 close 时：谓词及相对 `units.tsv` 与 `ledger.tsv` 的计数；各 track 与各自 landed 项；frontier（PR 列表加 SHA）；verdict 摘要；abandon 项及原因；待人类处理的 gate（唯一 ask）；store 路径与 trail 路径。数字来自表，非叙述。含 PR 链接。
