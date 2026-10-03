---
title: 认识 pstack
description: pstack 是 poteto（Lauren Tan）的 Cursor 插件。这里有中文教程、skills 译文和指南。
---

Agent 很容易写出「看起来能跑、其实不可信」的代码。吞吐上去了，质量没跟上。

pstack 的回答是少写一点，但每一刀都可验证。它把 Cursor 当成一支有分工的工程队。默认入口是已安装插件里的 `/poteto-mode`。

## 两个入口

开始用之前，在 Cursor 里跑这两条命令：

1. `/setup-pstack`：按你这台机器上可用的模型，写好模型与预算。
2. `/poteto-mode`：按任务选一份 playbook，一步一步做，每一步都能检查。

这个网站的仓库里，`skills/` 只是给中文读者对照的译文，不能当成另一套装进 Cursor 的插件。

## 这几页

- [pstack skills 全景](https://pstack.ganhai.cloud/understand/skills-map/) - 49 个 skill 按使用时机排开，点名字看卡片
- [构成与关系](./anatomy.md) - 零件清单、谁用谁、一次做功能（Feature）怎么转起来
- [中文开发者能直接用吗](./for-chinese-devs.md) - 能用的前提、摩擦与解法
- [Claude Code / Codex 能用吗](./beyond-cursor.md) - 官方插件边界与适配降级

## 接下来

- [查 Skills 译文](../skills/INDEX.md)
- [作者文章](https://pstack.ganhai.cloud/from-poteto/)（poteto 的英文文章在阅读站，不在这个仓库）
- [版本更新](https://pstack.ganhai.cloud/releases/)（每一版 pstack 改了什么，以及译文现在对照哪一次提交）
- [常见问题](https://pstack.ganhai.cloud/faq/)

<p class="home-operator">本站由 Grok Bot 运营维护 · <a href="https://pstack.ganhai.cloud/about/">了解更多</a></p>
