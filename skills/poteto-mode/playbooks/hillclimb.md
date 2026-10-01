### Hillclimb

**你负责指标与实验完整性。监督与审查，将尝试委托出去。** 用于一项可度量事物相对目标的持续迭代改进。一次性 fix 走 Bug fix 或 Perf issue。这是循环。

核心纪律：一次变更、一次测量、保留或还原。不要堆叠未测变更，不要凭读代码声称胜利（**prove-it-works** 原则 skill）。

1. 选指标前先确立工作负载与架构。对目标运行 **how** skill，列出可能移动结果的现实工作负载维度（数据规模、历史、状态、并发），并选择能复现用户抱怨的用例。若无用例能复现，先修复复现再 hillclimb。然后固定一项指标、何谓更好，以及可检查的停止谓词——将目标与尝试次数下限配对，以免早期侥幸改进结束运行（例如「至少比基线好 50% 且至少 10 次迭代」即此形状）。用户给数则用用户的，否则共同商定。
2. 构建测量 harness，证明其灵敏度，然后冻结（**build-the-lever** 原则 skill）。跑对比性的现实工作负载，确认目标用例复现症状而较易用例按预期分离。若 harness 无法区分，修订工作负载或指标。冻结后，一条可重复命令输出指标，采样足够以消除噪声（N 的中位数，非单次运行）。记录基线指标及 regression gate 的一次 green run（须持续通过的 tests），然后再做任何变更。
3. 通过 **show-me-your-work** skill 打开 decision log。`decision.tsv`，每次尝试一行：id、hypothesis、change、before、after、delta、tests、verdict（kept 或 reverted）、note。每次尝试前读取。不要放进 tree（gitignore）。
4. 每次假设须基于第 1 步架构模型，须命名具体机制（「因阻塞首次绘制而将 X 推迟到启动路径之外」），而非「试试给某处加缓存」。
5. 循环，每次迭代一个假设：
   - 将变更交给子代理，使用配置的 hillclimb 模型（默认 `grok-4.7-xhigh-fast`），范围收紧。监督并审查 diff，而非亲自敲代码（**guard-the-context-window** 原则 skill）。多个独立假设并行时，分发到并行子代理，各在独立 worktree（**separate-before-serializing-shared-state** 原则 skill）。
   - 用冻结 harness 测 before 与 after，并跑 regression gate。
   - 仅当指标越过噪声且 gate 仍绿时接受。否则完整还原。「可能有帮助」的微调不保留。
   - 每个接受的 fix 一个 commit，仅 stage 改动的文件（`git add <files>`，不要用 `-A`）。无论 kept 或 reverted 都记一行。
   每次迭代在下一次开始前以 check 结束（**sequence-verifiable-units** 原则 skill）。无人值守时，仅借用 Autonomous run playbook（`playbooks/autonomous-run.md`）的唤醒机制，不用其停止规则。
6. 突破首个平台期。停滞时——连续多次拒绝——切换类别、组合接近成功的方案、重读源码，或在认定 hill 已爬完前尝试更激进方案。正确性与简洁优先于数字。破坏行为的改进要还原，守住数字的简化要保留（**laziness-protocol** 原则 skill）。
7. 谓词满足时停止，或剩余想法边际价值不足时停止。不要放松谓词去「满足」它，廉价未试假设仍在时不要停止。卡住时上报问题，不要空转。
8. 用按落地顺序堆叠的 accepted commit 运行 **Opening a PR**。

**回复：** 指标与目标、基线到最终及百分比 delta、迭代次数（kept 与 reverted）、每个 accepted fix 一行、`decision.tsv` path，以及若继续推进会尝试的最佳想法。
