---
name: arena
description: "对同一任务并行生成 N 个候选方案，选定一个作为基底，把其余方案中最强的部分嫁接到其上。用于 /arena、「arena this」「throw it in the arena」，或当单次尝试非平凡产物会锁定错误形态时。"
disable-model-invocation: true
---

# Arena

对同一任务并行展开 N 次尝试。通读每个候选方案。选出最强者作为基底。把其余方案中的最佳思路嫁接到其上。验证合成结果。

## 开始

在启动任何工作之前，先打开 todolist，每个阶段一条。

1. 定框
2. 并行展开
3. 交叉评审
4. 选定基底
5. 嫁接
6. 验证

## 阶段 A：定框

N 个候选将收到相同 prompt，因此 prompt 就是契约。

1. 说明每个候选要产出的产物。
2. 推导评分标准。说明本任务的成功标准，再将其转化为 3–6 条可具体评分的准则。评分标准是阶段 D 选基底的工具。候选只看到任务本身。
3. 选择 runner。使用 `~/.cursor/rules/pstack-models.mdc` 中的 `arena runners` 行。若规则或该行缺失，默认各用一个：`claude-opus-5-5-max`、`gpt-5.6-sol-max`、`grok-4.7-xhigh-fast`。此行或交叉评审行中的 `auto` 或 `inherit-parent` 表示父模型，对其省略 `model`。若 Task 工具拒绝某条配置，则用该模型族的默认值运行并说明。模型族按前缀区分：`claude-*`、`gpt-*`、`grok-*`。若无匹配族，使用 `claude-opus-5-5-max`。若默认值也被拒绝，从其错误信息中选取同族最接近的有效 slug。当 arena 覆盖多个设计方向时增加 runner 数量。当工作是生成受限而非判断敏感时，同一模型跑 N 次。
4. 分配输出路径。每个候选写入各自位置（尽可能用 git worktree，否则 `/tmp/arena-<slug>/candidate-<n>/`），遵循 **separate-before-serializing-shared-state** 原则 skill。

## 阶段 B：并行展开

在一条消息中 spawn 全部 N 个子 agent，设 `run_in_background: true`，每个子 agent 收到任务、共享 grounding 路径、各自输出路径，以及产出产物和简短 rationale 的指令。

每条 rationale 须说明候选考虑过哪些备选，以及拒绝了什么。

若某候选未能产出，以 N-1 继续，并在 synthesis record 中注明 dropout。

## 阶段 C：交叉评审

阶段 B 全部候选完成后，从 `~/.cursor/rules/pstack-models.mdc` 的 `arena cross-judge pool` 行中选一个模型。若规则或该行缺失，从 `claude-opus-5-5-max`、`gpt-5.6-sol-max`、`grok-4.7-xhigh-fast` 中选择。优先选与父模型不同模型族的。在该模型上 spawn 一个只读 judge 子 agent。它看到评分标准和按路径标注的候选，逐条准则打分，并推荐基底及理由。它与父 agent 在阶段 D 的阅读并行，而非与候选并行。候选仍在写入时不要 spawn judge。

## 阶段 D：选定基底

选定前通读每个候选。

逐条准则对候选打分，不要凭整体感觉。与交叉评审对比。对基底一致则确认选择。不一致说明一方有偏见或评分标准模糊。决定前阅读双方 rationale。

选未来维护者最易在不破坏不变量前提下扩展的候选作为基底。若两个感觉相当，按 **laziness-protocol** 优先更清晰的边界或更小的 API。

在基底产物旁写简短 synthesis note，记录选择与理由，含交叉评审结论。

## 阶段 E：嫁接

再通读每个落选候选，找出值得移植到基底的内容。信号通常每个候选只有一两处，而非大部分。

按 **redesign-from-first-principles** 原则 skill 手工合并每项 graft。不要机械粘贴。结果须在同一心智模型下保持连贯。

记录嫁接内容、来源候选，以及拒绝内容及原因。

当 N 个候选收敛到同一形态，这是强一致信号。在 record 中注明收敛并交付共识形态。无需 graft。当 N 个候选严重分歧，阶段 A 规格不足。重新定框并重跑，而非平均化分歧。

## 阶段 F：验证

合成产物须经受与其他输出相同的审查，遵循 **prove-it-works** 原则 skill。

若验证发现问题而 arena 未捕获，要么阶段 A 有误（重新定框并重跑），要么某候选已捕获而你漏了 graft（回到阶段 E）。不要掩盖。

## 输出

一个合成产物。旁附一条简短 synthesis note，说明基底、graft（及来源候选）、拒绝项、dropout（若有）及验证结果。
