---
name: show-me-your-work
description: "为长期或无人值守工作保留可审查的决策轨迹：TSV 日志，每行一条决策（做了什么、为何、证据、结果）。默认本地；审查者需要轨迹才能信任结果时再 commit。用于 /show-me-your-work、自主或多阶段 run，或人类离开后再审查的工作。"
disable-model-invocation: true
---

# 展示你的工作

只保留一份权威日志。

## 格式

单个 TSV 文件，每行一条决策。单元格单行。证据是指针，不是散文。

复制 `references/decision-log-template.tsv`（表头行）开始新日志。列：

- **ts。** ISO8601 时间戳。
- **phase。** 阶段或工作流。
- **decision。** 选了或做了什么，一行。
- **why。** 平实理由。若原则驱动，平实说明，不要用术语标签。
- **evidence。** 证明它的链接或路径：commit SHA、PR 号、`file:line`、artifact、trace 或截图路径。不要段落。
- **result。** 结果或谓词状态：`tests green`、`reverted`、`pixel-diff 0`、`INCONCLUSIVE`、`open`。

示例，平实可读，审查者一眼能懂：

```
ts	phase	decision	why	evidence	result
2026-05-24T09:02:00Z	frame	counted the work first, about 100 components and roughly 75 hours	wanted to know the size before starting a long run	commit 3a9f1c2	found 5 things to sort out before starting
2026-05-24T09:40:00Z	harness	took screenshots of the old version before changing anything	so we can compare old against new and catch any visual change	scripts/snapshot.sh, baseline/	saved 120 reference screenshots
2026-05-24T11:15:00Z	widget	moved the widget styles over without changing how it looks	keep the change small and the result identical	commit 7c21e0a, pixel-diff 0	looks identical, tests pass
2026-05-24T12:30:00Z	widget	threw out a helper's work because its screenshots were blank	checked the real files instead of trusting its summary	worktree reset	reverted, tightened the instructions for next time
```

## 记一行

像跟队友说做了什么那样写每条。**unslop** skill 也适用于日志正文。

用 helper `scripts/log.sh <logfile> <phase> <decision> <why> <evidence> <result>`。它打 `ts`、首次写表头、去掉多余 tab/换行，并对以 `=`、`+`、`-`、`@` 开头的单元格加单引号前缀。裸 `printf` 追加一行也行，但生成或用户提供的文本同样注意这些字节。

只记决策点与检查点，不是每个动作：选了哪条岔路、完成某单元及验证结果、转向或 revert 及触发、已浮现的阻塞项、修好的 gate。循环 run 每迭代一行。跳过琐碎自明的。

一次 run 是一个 agent 对话，含后续轮次及其摘要。pickup、替换 agent 或新 chat 开新 run。run 往已有行的日志追加时，其第一行 phase 为 `start`；在另一 run 的 `start` 行之后的第一行也是 `start`。故 run 在后续轮次回到日志时，先读日志末几行看是否有其他 run 写入。`start` 行命名本 run 未写、其前的行 `ts` 范围，evidence 命名本 run（如 agent id）。`start` 不作他用。

## 存放位置

默认日志是工作 artifact，不 commit。放在工作目录 `decisions.tsv`，或并行多任务时用 `.audit/<task-slug>.tsv`，且不纳入 git。

仅当工作足够宏大、审查者需要轨迹才能信任结果时才 commit。

## 规则

- 只追加。错误决策用新行覆盖并注明。不要改删历史。
- 优先用已 commit 脚本产出的证据，而非手工一次性脚本（**encode-lessons-in-structure** 原则 skill）。

## 对照 transcript 审计日志

run 结束前、交回前，检查日志是否说了真话。读 active workspace 下 `agent-transcripts/` 中本 run 的 transcript（系统 prompt 给路径）。不要 glob `~/.cursor/projects/*/`。那会读到无关私密 chat。把本 run 各行与实际发生的事对照。每段从本 run 的一个 `start` 行开始（或本 run 建日志则从第一行），到下一 run 的 `start` 行结束：

- 每行是否对应真实决策或动作。
- 每行 evidence 是否可解析且支持该行声称。
- 塑造工作但未记录的分叉、转向或放弃路径是缺口。补上。

修正日志，不改故事。审计从不编辑或删行，含捏造的行。某行既非真实决策也非真实动作，或声称/evidence 有误，则追加覆盖行，写实际发生的事与可解析指针。本审计不查本 run 段外的行。若本 run 工作显示段外某行有误，像任何错误记录一样追加覆盖行。

## 跨模型审查轨迹

交回前，在与做工作的模型不同 model family 上 spawn 子 agent。自审不能替代。子 agent 读审计轨迹与本 run transcript，标出用户应留意的点。不是重做工作，是扫次优或风险之处。

- 证据弱或缺失的决策。
- 跳过或 transcript 无证明的验证步骤。
- 事后看 risky 的选择（过早、范围蔓延、掩盖症状）。
-  粗读会漏的缺口。

产生轨迹的 run，每次回复末尾加「Attention」节。先单独一行 reviewer 模型（`reviewed by <model>`），再列各 flag 指向具体行或时刻。「No flags」有效。模型名本身不是 flag。

## 阅读轨迹

自上而下读，跟 evidence 指针，抽查。GitHub 把 committed TSV 渲染成表。终端用 `column -s$'\t' -t decisions.tsv`。

## 组合本 skill

其他 skill 按名把审计轨迹路由到这里，不要自造格式。不要复述列定义。
