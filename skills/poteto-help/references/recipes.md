---
description: 值得照抄的提示词。把路径、skill 和完结条件换成你自己的。写得随便也行。
---

:::note[导读]
一组可以照抄的提示词。把路径、skill 和完结条件换成你自己的。
:::

# 值得照抄的提示词

把真实的路径、skill 和完结条件换进去。写得随便也行。

## 弄清

- `/poteto-mode read <thread>. restate the underlying issue in your own words, in plain english.`
- `/poteto-mode investigate why <symptom>. give me what we know, what data you used, and your best hypotheses. don't change any code yet.`
- `use /how to understand <subsystem>. then use /why to find out why it broke recently.`
- `/recall my work on <topic> from last week, then read <issue>.`
- `/teach me why you implemented it this way and not <other way>. what did you trade off?`
- `/poteto-mode take over this branch. read the decision log, find what's done, and continue. don't redo finished work.`

## 构建

- Bug：`/poteto-mode <symptom>. repro first, then fix and verify.`
- 应用里的 bug：`/poteto-mode repro this with /verify-<app>. if it repros on main, fix it and show me a video as proof.`
- 有便宜测试的 bug：`/poteto-mode repro <bug> first. if there's a cheap test path, /tdd it. then fix and rerun.`
- 功能：`/poteto-mode add <behavior>. <current output> stays byte-identical. verify both.`
- 重构：`/poteto-mode move <code> into one module, zero behavior change. record the current output first and prove it's unchanged after.`
- 性能：`/poteto-mode <operation> takes <time> on <fixture>. trace it, fix the measured cause, show me before and after.`

## 设计和计划

- `/poteto-mode prototype a few options for <feature>. take screenshots or videos for me to compare.`
- `/poteto-mode we need <feature>. /architect it first, and answer open questions with prototypes. let me review before proceeding.`
- `/poteto-mode write a tutorial for how i would use <new package> first. then /teach me why it beats the current one.`
- `ask /arena for a second opinion on this thread and our approach.`
- `/poteto-mode turn this design into a plan. small verifiable PRs, each with its own verification steps.`
- `/poteto-mode plan the migration of <library> to <target>. small verifiable PRs. the result must match the original exactly, bugs included.`

## 审查并交付

- `/interrogate the whole branch, but skeptically. don't change anything yet. no nitpicks unless it's a real bug or regression.` 驳回的那些也要读。
- `/swarm check every package under <dir> against its check script. one worker per package. one report.`
- `/poteto-mode open the pr. small ordered commits, evidence in the description.`
- `/poteto-mode babysit this pr. get it green.` 只问现状时：`/poteto-mode check on pr <number>. anything outstanding?`
- `/poteto-mode land the stack.`

## 离开再回来

- `/poteto-mode im going to bed. <goal> in a fresh worktree off <base>. done means <checks>. keep a decision log. don't ask me before committing. /loop until done. if you're truly stuck after a few hours, stop and write up why.`
- `/show-me-your-work catch me up on what you did last night.` 先读它的 Attention 一节。
- `/poteto-mode full autopilot on this queue. each item is independent.`
- `/poteto-mode autopilot these changes but stack them, don't ship. i'll land the stack.`
- `/reflect capture what we learned so the next run doesn't repeat it.` 只批准会改变以后某个决定的那些改动。
- `/bro` 用大白话重说上一条回复。
