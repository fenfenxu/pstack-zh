---
name: maintain-verification-skill
description: "定期 pass，保持项目 verification skill 与 feature map 诚实：每功能并行只读 source reader、一次 live session 驱动全部功能、最多一个 PR 的已证明修正。用于 /maintain-verification-skill 或「audit the verify skill」。"
disable-model-invocation: true
---

# 维护 verification skill

应用一变，feature map 就开始腐化。本 skill 是 `/create-verification-skill` 生成 skill（或任何带 feature map 的项目本地 verification skill）的 upkeep 循环。严谨单位是功能，不是每句话：每个功能文件都要 source 覆盖与 live 演练，不必把每条 bullet 终端化。

## 结果

选一种并说明：

- **clean** — 每功能都有 source 与 live 覆盖；无值得 ship 的。无分支、无 PR。
- **changed** — 一个 PR ship 已证明的文档、harness 或 map 修正。
- **blocked** — 覆盖无法完成或已证明修复无法安全 ship。精确说明阻塞点。

## 编辑范围

只编辑 verification skill 自有目录（其 SKILL.md、features/ 及自有 harness 脚本）。run 中不要改产品代码：map 描述的行为应用不再做，要么是文档漂移（修 map），要么是产品回归（报告，不要在文档里掩盖）。

## Pass

0. **定位目标。** 找要维护的 verification skill：正文含 launch/drive 与 feature map 的项目本地 skill（通常 `.cursor/skills/verify-*/`）。多个候选则问哪一个；没有则停，指向 `/create-verification-skill`，不要发明目标。

1. **索引卫生。** 读 feature map README 并 glob 兄弟文件。修缺失、多余、重复或 dead 条目。轻量；不要生成 inventory。

2. **Source 波。** 每个功能文件一个只读子 agent，并发 launch。各从 source 解释「该面向用户功能如何工作」，带引用 flag 可能文档漂移，返回一条 concise live 验证配方。子 agent 不驱动应用、不编辑文件。返回形态：功能摘要 / source 入口 / 可能漂移或无 / 一条配方。

3. **对账。** 每个功能文件都有返回摘要。把重叠配方合并为尽可能少的应用状态。 spot-check 引用的漂移；不要重证 clean 声称。扫近期 churn 找 map 缺失的用户面；称缺失须有具体 source 路径。

4. **Live pass。** 即使 source 看起来 clean 也必需。协调者拥有全部 driving；遵循 verification skill 自有 launch 模型——server 与 UI 一个长驻实例串行驱动，或短生命周期 CLI 每次 drive 新隔离 session（由 skill 的 Launch 节决定，不是本 skill）。至少演练每个功能一次，全程 hold 三条 invariant，无论失败：(1) 不要驱动自上次做 surprising 之事以来未 health-check 的实例——首次 drive 前 doctor，session 为单元时每新 session doctor，任何失败 drive 后再 doctor；doctor 看不到失败时（健康进程上的 wedged UI），reset 到已知状态或 relaunch，不要碰运气；(2) 迄今采集的证据 survive 每次 cleanup，在其指名位置检查，不要假设；(3) drive 启动的东西不要比该 drive 有用性活得更久——失败迭代 residue 无论 session stuck、退出或共享都要清（共享实例清 residue，不清实例）。skill 漂移导致的 doctor 失败是漂移：在编辑范围内修并重试一次——只 restart 修复 invalidate 的部分——再称 pass `blocked`。功能不可达仅当给出具体 prerequisite（auth、entitlement、OS、外部状态）与尝试路径时为 `verified-unreachable`；map 缺该 prerequisite 是漂移。triage 的任何 harness 修复 ship 前须 live 重驱动。最终 teardown 在 run 最后一次 drive 之后——含那些 re-proof——使无物 outlive run（证据保留，按 skill）。

5. **Triage。** 错误或缺失的用户 POV 描述 → 文档漂移，修它。行为正常但 harness 驱动不了 → harness gap，修它；harness 修复遵循与生成相同的 helpers 规则（脚本可执行、skill 正文写调用）。应用行为真坏了 → 产品 gap；记给用户，不进本 PR。

6. **Ship 或停。** changed：一个 PR 的已证明修正，先重读每个改动文件。clean 或 blocked：无 PR，诚实报告结果与覆盖。

在 scratch 位置保留 concise run notes（覆盖功能、不可达 prerequisite、确认漂移、结果）；不要 commit。
