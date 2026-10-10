---
title: 中文开发者能直接用吗
description: 能。前提是 Cursor 与官方插件。摩擦在英文运行时与模型阵容，不在「必须会英文才能点开」。
meta:
  updated_at: "2026-10-04T20:40:25+08:00"
  updated_by: "cursor-cloud-agent cursor"
  triggered_by: "pstack-daily-translate routine"
---

## 结论

**能直接用。** 前提是本机 Cursor，并安装官方插件 `pstack`（`/add-plugin pstack`）。

本站是中文**阅读与学习**站。不是汉化插件，也不二次分发官方插件。你跟练时，slash 命令与运行时正文仍来自插件里的英文 skills。

## 摩擦

| 现象 | 原因 | 影响 |
|---|---|---|
| Skill 打开是英文 | 插件交付物是英文 `SKILL.md` | 跟读吃力。命令与步骤名仍须认英文 |
| 默认要 opus / grok / sol 等 slug | poteto-mode 与 panel 按多模型分工 | 本机能用的模型不全时，Task 会拒绝所选模型，或一次次改用别的模型 |
| 想「装中文 skills 当运行时」 | 这个网站的仓库里，`skills/` / `content/skills-zh/` 是译文 | 和官方插件一起注册会冲突，两边还会越差越远 |
| 回复与 playbook 术语偏英文工程话 | 官方文体与原则名是英文 | 中文提问没问题。对照表与原则名仍要认得 |

中文提问、中文讨论完全可以。卡点通常是**读规程**与**模型是否可用**，不是「界面没有中文就不能跑」。

## 怎么解

1. **读用中文，跑用插件。** 先在本站 [Skills 译文](../skills/INDEX.md) 与 [构成与关系](./anatomy.md) 建立地图。真正执行只调用已安装插件。
2. **跑 `/setup-pstack`。** 按本机可用 slug 写 `~/.cursor/rules/pstack-models.mdc`。模型少时，相关角色设成 `inherit-parent` 或 `auto`，跟父聊天同一模型。
3. **不要**把这个网站的仓库里的 `skills/` 链进 Cursor，当成第二套插件。对照阅读可以。盖住官方插件的安装路径不行。
4. **顺着本站练。** [认识 pstack](./index.md) 讲两个入口命令，[构成与关系](./anatomy.md) 讲任务怎么走到 playbook。目标是会用这两个命令，以及怎么把任务送到对应的 playbook，不是背 51 个 slash。

## 本站不承诺什么

- 不提供「全中文运行时 pstack」。
- 不保证某家国内模型一定出现在默认角色表里。能进表的，以 setup 检测到的 slug 为准。
- 译文保留 `name`、路径、命令、模型 slug、状态枚举、产品名。这些不要「翻译掉」。

## 接下来

- [构成与关系](./anatomy.md)
- [Claude Code / Codex 能用吗](./beyond-cursor.md)
