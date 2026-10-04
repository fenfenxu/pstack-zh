---
title: 认识 pstack
description: pstack 是 poteto（Lauren Tan）开源的一套 Cursor agent skills，放在 GitHub 仓库 cursor/plugins 的 pstack/ 目录，MIT 许可。
---

pstack 是 poteto（Lauren Tan）开源的一套 Cursor agent skills，放在 GitHub 仓库 [cursor/plugins](https://github.com/cursor/plugins) 的 `pstack/` 目录，MIT 许可。

它的核心思路很简单：别让 agent 一味多写代码，先把事情做深、做准，再谈速度。

默认入口是已经安装好的 `/poteto-mode`。

本站是非官方中文学习站，主要用来对照阅读 pstack 的中文译文，不发行中文版插件。

## 作者


## pstack 是干什么的

现在用 agent 写代码，一个很常见的问题是：代码产量上去了，但质量不一定跟得上。

poteto 在 README 里说得很直接：她不想像一支“二十人烂代码团队”那样交付。只有吞吐，没有质量，不是她想要的结果。

pstack 就是她给出的做法。

它不是为了让 agent 写更多代码，反而是希望它少写一点，但写得更好，而且结果能被验证。

另一个重点是并行。

如果你能相信一个 agent 会认真分析、写出靠谱代码，并且留下足够的验证证据，就可以放心同时开多个 agent 做事。pstack 里不少流程也是围绕这个思路设计的。

它也不绑定某一个模型。不同模型擅长的事情不一样，pstack 会把不同任务分给更合适的模型；这些配置也可以自己改。

## 两个入口

第一次用，先跑这两个：

1. `/setup-pstack`
2. `/poteto-mode`

`/setup-pstack` 用来配置模型和 reasoning budget。它会根据你当前能用的模型生成配置。

`/poteto-mode` 是平时最常用的入口。遇到需要认真分析、实现、验证的任务时，从这里开始就行。

它会先判断任务属于哪一类，再选对应的 playbook，然后按步骤调用其他 skills。

如果你只是刚开始接触 pstack，不需要先把几十个 skill 全看一遍。大部分时候直接用 `/poteto-mode` 就够了。

这个网站仓库里的 `skills/` 是给中文读者对照看的译文，不是另一套可以直接装进 Cursor 的插件。

## 这个站里有什么

- [pstack skills 全景](https://pstack.ganhai.cloud/understand/skills-map/)

  把所有 skills 放到一张图里，按用途和使用时机来看。

- [构成与关系](./anatomy.md)

  看 pstack 由哪些部分组成，以及一次完整任务里这些部分怎么配合。

- [中文开发者能直接用吗](./for-chinese-devs.md)

  讲实际使用时会遇到哪些问题，以及怎么处理。

- [Claude Code / Codex 能用吗](./beyond-cursor.md)

  说明哪些东西是 Cursor 专用的，哪些思路可以迁到别的 agent 环境里。

## 继续看

- [查 Skills 译文](../skills/INDEX.md)
- [作者文章](https://pstack.ganhai.cloud/from-poteto/)
- [版本更新](https://pstack.ganhai.cloud/releases/)
- [常见问题](https://pstack.ganhai.cloud/faq/)

“作者文章”里放的是 poteto 的英文原文。

“版本更新”会记录 pstack 每一版改了什么，以及本站译文当前对照的是哪一次提交。

<p class="home-operator">本站由 Grok Bot 运营维护 · <a href="https://pstack.ganhai.cloud/about/">了解更多</a></p>
