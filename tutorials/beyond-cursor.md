---
title: Claude Code / Codex 能用吗
description: 官方插件不能原样装。可移植的是规程与原则。Cursor 专属钩子要重写或降级。
---

## 结论

**官方 pstack 插件不能原样装进 Claude Code 或 Codex。** 安装面是 Cursor 插件（`skills` + `agents`）。没有给 Claude Code / Codex 的官方发行包。

可搬走的是**规程与原则**。不能假装带走的是 **Task / 专用 agent 类型 / Cursor rules 路径 / sticky mode** 这一整套编排。

下文的「Claude Code」指 Anthropic 的 coding agent。「Codex」指 OpenAI Codex CLI 一类。若你指的是别的产品，边界仍相同：没有 Cursor 的 Task 与插件 agents，完整 pstack 就不会按设计跑。

## 硬依赖（换宿主会断）

这些步骤或配置点名 Cursor 能力。换到 Claude Code / Codex 会缺工具或行为漂移。

| 依赖 | 在 pstack 里干什么 |
|---|---|
| 插件清单 | 只注册 `skills/` 与 `agents/`。用 `/add-plugin pstack` 安装 |
| `Task` + `subagent_type` | 扇出子代理。playbook 内写代码默认 `poteto-agent` |
| `poteto-agent` / `Comment Sicko` | 插件 agents。换 `generalPurpose` 会跳过 poteto-mode 精读 |
| `~/.cursor/rules/pstack-models.mdc` | `/setup-pstack` 写入。覆盖各角色模型与 panel 人数 |
| `AskQuestion` | setup 预算确认、部分偏好分叉 |
| sticky `mode: true` | `/poteto-mode` 跨回合保持 |
| `/loop` | 过夜与长时轮询（Cursor 内建，不是 pstack skill） |
| `cursor-team-kit` | `/deslop`、`control-ui` / `control-cli` 等 |
| transcript / agent store 路径 | `recall`、`reflect`、orchestrate 状态等 |

`swarm` 的 cloud worktree、`babysit` / `shipping` 对 Bugbot 与 forge CLI 的写法，同样绑在 Cursor 产品面上。

## 可移植层

这些内容的**决策语义**不依赖 Cursor API。frontmatter 里的 Cursor 包装可以剥掉，正文仍可读。

| 层 | 怎么用在别处 |
|---|---|
| **24 条原则** | 写成项目规则或 skill。例如 prove-it-works、sequence-verifiable-units、explain-the-number |
| **Playbook 步骤** | 当成一份核对清单：先复现，再查根因，然后一小步一小步验证，最后开 PR。 |
| **文体** | unslop、technical-writing、短句证据同句 |
| **验证文化** | 对真实产物证明。能编译不算完成 |
| **figure-it-out 思路** | 没有窄规程时，先设计可审计阶段再跑 |

偏程序、Cursor 钩子较少的 playbook 包括 investigation、feature、refactoring、prototype、perf / hillclimb、forensics、worktree-cleanup。钩子仍可能出现在收尾（开 PR、overnight）。

## 如何适配

目标不是「逐字兼容」。目标是保留**可验证、可分工**的工程习惯，并诚实降级做不到的扇出。

### Claude Code

1. 把高频原则与窄工作流收成 `SKILL.md`（或项目 `CLAUDE.md` 里的硬约束）。
2. 用 Claude 自己的子代理 / skill 调用，**重写**「spawn Task」段落。不要保留 `subagent_type: poteto-agent`。
3. 多模型 panel（arena / interrogate）若只有一家模型，改成「同模型分角色 prompt」或人工第二意见。写明已降级。
4. 验证 skill 落到 Claude 约定的 skills 目录，而不是 `.cursor/skills/`。

### Codex

1. 仓库级约定放进 `AGENTS.md`（Codex 与多工具常见入口）。
2. 可复用步骤用 Codex 支持的 skill / 手册格式承载。
3. 模型表不要抄 `pstack-models.mdc` 文件名。按 Codex 自己的模型配置，写一版每个角色对应哪一款模型的对照即可。
4. 没有 sticky mode 时，每个会话开头显式「按 Feature playbook 执行」并贴步骤。

### 共通降级表

| Cursor 上的能力 | 适配策略 |
|---|---|
| `/poteto-mode` sticky | 会话首条固定规程 + 检查清单 |
| `poteto-agent` | 宿主默认子代理 +「先读原则索引」指令 |
| arena / interrogate 多模型 | 单模型多角色，或接受无 panel |
| `/setup-pstack` | 手写一份「每个角色用哪一款模型」的表，或用宿主里等价的配置。 |
| `/loop` + babysit | 宿主自己的定时 / 轮询，或人工回看 |
| control-ui / CDP 证明 | 换成该宿主能驱动的浏览器或 CLI 检查 |

## 本站与官方不承诺什么

- 不维护「pstack for Claude Code」或「pstack for Codex」发行版。
- 不保证把英文 skill 目录复制进其他宿主就能获得相同行为。
- learn-pstack 帮助中文读者**理解**官方 Cursor 工作流。跨宿主移植是你的工程选择，不是本站交付物。

## 接下来

- [构成与关系](./anatomy.md)
- [中文开发者能直接用吗](./for-chinese-devs.md)
- 在 Cursor 里先跑通 [两个入口](./index.md) 后再谈移植
