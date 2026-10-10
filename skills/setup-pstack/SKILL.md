---
name: setup-pstack
description: 配置 pstack 各角色使用的模型及推理预算。检测可用模型并写入始终生效的规则以覆盖 skill 默认值。用于 /setup-pstack、「configure pstack models」「pstack budget」，或更改 pstack 模型选择时。
---

# Setup pstack

写入 `~/.cursor/rules/pstack-models.mdc`，一条始终生效的规则，按角色设置 pstack 模型。

## 步骤

### 1. 检测可用模型

枚举本 session 中可传给 `Task` 子 agent 的 model slug。这是最可靠的来源。若 Cursor 还暴露列出用户 entitled 模型的 models API 或 CLI，优先用它以求完整。若检测不到任何 slug，请用户粘贴其可用的 slug。切勿写入未确认可用的真实 slug。别名 `inherit-parent` 与 `auto` 始终有效，尽管它们不是检测到的 slug。

### 2. 加载当前状态

默认角色到模型的映射即下方步骤 5 的规则形态。若 `~/.cursor/rules/pstack-models.mdc` 已存在，读取它，将其 `# budget` 行与各 role 值视为当前选择。否则从默认开始。步骤 5 中不存在的 role 行（如 `how critics`）来自已退役 role，删除。

### 3. 预算、映射与确认

**(a) 询问预算。** 优先用 AskQuestion，不用自由文本。提供下列四个选项，标签须完全一致；若规则中已有预算则点明当前预算。没有规则时，说明 `large` 与 skill 默认值一致。

- `unlimited — max reasoning`
- `large — xhigh reasoning`
- `medium — high reasoning`
- `small — medium reasoning`

**(b) 应用预算。** 从 skill 默认值构建工作表；重跑时保留你按族、列表或别名（`inherit-parent`、`auto`）改过的 role。`unlimited`、`large`、`medium`、`small` 将每个真实 slug（含 panel 条目）的 effort token 设为 `max`、`xhigh`、`high` 或 `medium`。effort token 是最后一个 token，或在末尾 `fast` 之前的那个，阶梯为 `max` > `xhigh` > `high` > `medium` > `low`。若结果不是检测到的 slug，用同族中 effort 不高于目标的最高 detected slug，否则将该 role 标为需选择。`inherit-parent` 与 `auto` 不变。故 `unlimited` 会把 `claude-opus-5-5-xhigh` 变为 `claude-opus-5-5-max`。Grok 的 slug 最高到 `xhigh`，所以在 `unlimited` 下回退会把 Grok 放到 `xhigh`，`grok-4.7-xhigh-fast` 保持不变。`large` 保持这两个默认。`small` 会把它们变为 `claude-opus-5-5-medium` 和 `grok-4.7-medium-fast`。

**(c) 展示 role 并确认。** 展示每个 role 及其模型，将 detected 集合外的真实 slug 标为需选择。同时列出步骤 2 删掉的行。问是否按现状接受或改特定 role，选项为 detected 模型加 `inherit-parent` 与 `auto`（二者均表示该 role 跑在父聊天模型上，Auto 用户借此保持 Auto）。优先 AskQuestion。panel role（arena runners、architect runners、interrogate reviewers）的值为列表，每个条目 spawn 一个子 agent，含别名条目，列表长度即数量。`arena cross-judge pool` 也是列表，但 Arena 会从中选一个与父模型不同 model family 的值（若可能）。`swarm workers` 是每个 worker 的默认模型，除非 race 或 comparison 为各 arm 指定其他模型。

### 4. 校验

写入的每个真实 slug 须在 detected 集合内。`inherit-parent` 与 `auto` 始终通过。若所选真实 slug 不可用，停止并再次询问。

### 5. 写入规则

写入 `~/.cursor/rules/pstack-models.mdc`，`alwaysApply: true`，`# budget` 行为所选标签及其目标 effort，每个 role 一行，标签与 poteto-mode 一致。覆盖整个文件使重跑幂等。形态：

```
---
description: pstack per-role model choices (overrides skill defaults)
alwaysApply: true
---
# pstack model configuration. One line per role. Delete a line to fall back to the skill default.
# `inherit-parent` or `auto` as a value: the role runs on the parent chat model (omit Task `model`). Alias entries in a panel list still count toward its fan-out.
# budget: large (xhigh)
feature, refactoring: grok-4.7-xhigh-fast
bug-fix: grok-4.7-xhigh-fast
perf-issue: grok-4.7-xhigh-fast
hillclimb: grok-4.7-xhigh-fast
judgment and prose: claude-opus-5-5-xhigh
hardest tasks: claude-opus-5-5-xhigh
how explorer: grok-4.7-xhigh-fast
how explainer: claude-opus-5-5-xhigh
why investigators: grok-4.7-xhigh-fast
why synthesizer: claude-opus-5-5-xhigh
reflect tooling: grok-4.7-xhigh-fast
reflect judgment, divergent, synthesizer: claude-opus-5-5-xhigh
arena runners: claude-opus-5-5-xhigh, grok-4.7-xhigh-fast
arena cross-judge pool: claude-opus-5-5-xhigh, grok-4.7-xhigh-fast
swarm workers: grok-4.7-xhigh-fast
architect runners: claude-opus-5-5-xhigh, grok-4.7-xhigh-fast
interrogate reviewers: claude-opus-5-5-xhigh, grok-4.7-xhigh-fast
```

### 6. 确认

告知用户规则已写入，对新 session 生效。重跑本 skill 会更新它。

### 7. 提供 verification skill（可选）

检查项目是否有驱动真实应用作证明的方式（`verify-*` skill 或现有 harness）。若无，提供一次：「要不要项目本地 verification skill，让 agent 像用户一样驱动应用并证明改动有效？我可以用 /create-verification-skill 生成。」若同意，调用 `/create-verification-skill`（解析 pstack 安装位置：workspace、user 或 plugin）。若否，继续，不反复推销。
