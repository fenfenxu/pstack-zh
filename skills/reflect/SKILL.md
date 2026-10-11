---
name: reflect
description: 对当前对话 transcript 并行 spawn 三个评审子 agent，浮现可沉淀经验，并将每条路由到既有 skill 的具体编辑。用户说 reflect 时使用。
disable-model-invocation: true
---

# Reflect

从当前对话中挖掘可长期保留的经验，并路由到 skill 编辑。

用户说「reflect」或「/reflect」时调用。对话琐碎、偏题，或父 agent 已正确遵循既有 skill 时跳过。一次性事项不是经验。

## 1. 定位活跃 transcript

展开子 agent 之前，先在系统 prompt 给出的 `agent-transcripts/` 目录里找到这次对话的 transcript。不要 glob `~/.cursor/projects/*/`。那会跨工作区边界并读到无关项目的私有聊天。

```bash
ls -t <agent-transcripts>/*.jsonl <agent-transcripts>/*/*.jsonl <agent-transcripts>/*/subagents/*.jsonl 2>/dev/null | head -10
```

这覆盖了 legacy 扁平（`<id>.jsonl`）、当前嵌套（`<id>/<id>.jsonl`）和子 agent（`<parent>/subagents/<child>.jsonl`）三种布局。对每个候选，读 JSONL 第一行。取 `message.content[0].text` 含对话开场用户 prompt 的那份文件。没有对上的，就传一份紧凑的会话摘要。

## 2. 并行 spawn 三名评审者

一条消息、三次 `Task` 调用，`subagent_type: generalPurpose`，agent 模式（`readonly: false`）。评审者需要 MCP，才能去查 transcript 引用的工单、聊天串和可观测 trace。Readonly 会剥离 MCP。

各评审者与合成器对应 `pstack-models.mdc` 规则中的一行角色及默认值。将 `model` 设为该行值；缺失则用默认。值为 `auto` 或 `inherit-parent` 时不设 `model`。Task 拒绝 slug 则用默认并说明。默认也被拒绝则从错误信息取同系列最近有效 slug。

| 视角 | 角色行 | 默认 `model` | Prompt 模板 |
|---|---|---|---|
| 判断 | `reflect judgment, divergent, synthesizer` | `claude-opus-5-5-xhigh` | `references/judgment-reviewer.md` |
| 工具 | `reflect tooling` | `grok-4.7-xhigh-fast` | `references/tooling-reviewer.md` |
| 发散 | `reflect judgment, divergent, synthesizer` | `claude-opus-5-5-xhigh` | `references/divergent-reviewer.md` |

各模板原样传入，在标记处替换 transcript 路径或摘要。评审者在 `Task` 响应体中返回发现。

## 3. 合成

一次 `Task` 调用，`subagent_type: generalPurpose`，`model` 来自 `reflect judgment, divergent, synthesizer` 行（默认 `claude-opus-5-5-xhigh`），agent 模式（`readonly: false`）。它通过 MCP 抽查引用，Readonly 会剥离 MCP。把 `references/synthesizer.md` 原样传进去，在标记处内联各评审者的完整输出。它返回一份 Accepted / Rejected / Backlog 列表。

## 4. 结构性强制检查

Accepted 里如果有一项用 lint 规则、脚本、metadata 标志或运行时检查能更可靠地强制，就把它移到 Backlog。见 **encode-lessons-in-structure** 原则 skill。

## 5. 应用

应用任何 Accepted 编辑之前，先把合成器完整的 Accepted / Rejected / Backlog 输出拿给用户，等明确批准。用户选子集，也可以改路由。Skill 变更影响组织里以后的每个 agent。不要自动应用。

每条 Backlog 直接记到团队的 devex 或 backlog tracker，不用等。只有 Accepted 列表等待批准。

已批准的每一行，严格按它的 Routing：

- 既有 skill 的琐碎编辑（一行 bullet、收紧一句、纠正一个过时事实）：父 agent 直接做。
- 既有 skill 实质性编辑（新节、新模式表、超过约 10 行）：交给 Cursor 内置 `create-skill` skill 及其 draft / test / iterate 循环。
- `tune description: <skill path>`（skill 存在但未在应触发时触发）：交给 `create-skill` 及其 description 优化循环。
- `new skill via create-skill: <kebab-name>`：创建交给 `create-skill`。不要随意发明形态。

若环境提供 SKILL.md validator，每个改过的 skill 在宣布完成前跑一遍。

## 6. 向用户摘要

短列表，无开场废话：

- 已应用编辑：`<skill path>`。每项一行改了什么。
- 新建 skill：`<skill path>`。每项一行（少见）。
- 已归档到 devex tracker 的 Backlog：`<issue title>`（`<tags>`）。每项一行。
- 丢弃：每条 rejected 发现 + 合成器原因，一行。
