---
name: reflect
description: 对当前对话 transcript 并行 spawn 三个评审子 agent，浮现可沉淀经验，并将每条路由到既有 skill 的具体编辑。用户说 reflect 时使用。
disable-model-invocation: true
---

# Reflect

从当前对话中挖掘可长期保留的经验，并路由到 skill 编辑。

## 何时调用

用户说「reflect」或「/reflect」时调用。对话琐碎、偏题，或父 agent 已正确遵循既有 skill 时跳过。一次性事项不是经验。

## 流程

### 1. 定位活跃 transcript

父 agent 在展开子 agent 前先找自己的 transcript 文件。系统 prompt 会给出当前工作区的 `agent-transcripts/` 目录路径。用该路径。不要 glob `~/.cursor/projects/*/`。那会跨工作区边界并读到无关项目的私有聊天。

```bash
ls -t <agent-transcripts>/*.jsonl <agent-transcripts>/*/*.jsonl <agent-transcripts>/*/subagents/*.jsonl 2>/dev/null | head -10
```

三种 transcript 布局：legacy 扁平（`<id>.jsonl`）、当前嵌套（`<id>/<id>.jsonl`）、子 agent（`<parent>/subagents/<child>.jsonl`）。

对每个候选，读 JSONL 第一行，检查 `message.content[0].text` 是否含对话开场用户 prompt。取匹配路径。若无路径可解析，写紧凑会话摘要并传该摘要。

### 2. 并行 spawn 三名评审者

一条消息、三次 `Task` 调用，`subagent_type: generalPurpose`，`model` 如下，agent 模式（`readonly: false`）。评审者需 MCP 查 transcript 引用的上下文（工单、聊天串、可观测 trace）。Readonly 会剥离 MCP。

各评审者与合成器对应 `pstack-models.mdc` 规则中的一行角色及默认值。将 `model` 设为该行值；缺失则用默认。值为 `auto` 或 `inherit-parent` 时不设 `model`。Task 拒绝 slug 则用默认并说明。默认也被拒绝则从错误信息取同系列最近有效 slug。

| 视角 | 角色行 | 默认 `model` | Prompt 模板 |
|---|---|---|---|
| 判断 | `reflect judgment, divergent, synthesizer` | `claude-opus-5-5-xhigh` | `references/judgment-reviewer.md` |
| 工具 | `reflect tooling` | `grok-4.7-xhigh-fast` | `references/tooling-reviewer.md` |
| 发散 | `reflect judgment, divergent, synthesizer` | `claude-opus-5-5-xhigh` | `references/divergent-reviewer.md` |

各模板原样传入，在标记处替换 transcript 路径或摘要。评审者在 `Task` 响应体中返回发现。

### 3. 合成

一次 `Task` 调用，`subagent_type: generalPurpose`，`model` 来自 `reflect judgment, divergent, synthesizer` 行（默认 `claude-opus-5-5-xhigh`），agent 模式（`readonly: false`）。合成器质量检查含抽查验证引用，可能需要 MCP。Readonly 会剥离 MCP。原样使用 `references/synthesizer.md`，在标记处内联各评审者完整输出。合成器返回结构化 Accepted / Rejected / Backlog 列表。

### 4. 结构性强制检查

对合成器 Accepted 列表做合理性检查。若某项用 lint 规则、脚本、metadata 标志或运行时检查能更可靠强制，从 Accepted 移到 Backlog。见 **encode-lessons-in-structure** principle skill。

### 5. 应用

应用任何 Accepted 编辑前，向用户呈现合成器完整 Accepted/Rejected/Backlog 输出并等待明确批准。用户选子集应用并可重定向路由。Skill 变更影响组织内未来每个 agent。不要自动应用。

Backlog 项自动归档到团队使用的 devex / backlog tracker。仅 Accepted 列表等待批准。

对每个已批准 Accepted 项，严格按 Routing 字段：

- 既有 skill 琐碎编辑（一行 bullet、收紧句子、纠正过时事实）：父 agent 直接做。
- 既有 skill 实质性编辑（新节、新模式表、超过约 10 行）：交给 Cursor 内置 `create-skill` skill 及其 draft / test / iterate 循环。
- `tune description: <skill path>`（skill 存在但未在应触发时触发）：交给 `create-skill` 及其 description 优化循环。
- `new skill via create-skill: <kebab-name>`：创建交给 `create-skill`。不要随意发明形态。

若环境提供 SKILL.md validator，每个修改过的 skill 完成前跑一遍。没有则跳过。

### 6. 向用户摘要

短列表，无开场废话：

- 已应用编辑：`<skill path>`。每项一行改了什么。
- 新建 skill：`<skill path>`。每项一行（少见）。
- 已归档到 devex tracker 的 Backlog：`<issue title>`（`<tags>`）。每项一行。
- 丢弃：每条 rejected 发现 + 合成器原因，一行。
