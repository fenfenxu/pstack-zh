---
name: figure-it-out
description: "当没有更窄 playbook 适用时，设计可审计的 playbook：大型迁移、雄心勃勃的多部分变更，或人类离开后再审查的工作。按任务缩放严谨度，跑假设循环，经 show-me-your-work 记录决策。用于 /figure-it-out、「figure it out」、大型迁移，或无更窄 playbook 时。"
disable-model-invocation: true
---

# Figure it out

任务不匹配任何 playbook 时，设计一个。任何代码前的交付物是工作流本身：按任务缩放严谨度的阶段序列，跑科学方法，留下人类离开后能审计的决策轨迹。

## 开始

打开 todolist，第一项是阅读 **poteto-mode** skill 的 Principles 节。再把下列阶段加为 todo。

## 阶段 A：定框

先摸底，再承诺。直到能陈述以下内容再开始 run：

- 完成定义：可证伪谓词（**prove-it-works** 原则 skill）。
- 范围量化：大致单元与工作量，加摸底 surfaced 的 blocker。
- 严谨度，偏高默认。单向门与高 blast radius 多给。可逆低 stakes 步骤少给。严谨是 gate 与 artifact，不是「更努力试」。

长 run 前先呈现 framing 与权衡。可逆工作可继续（**never-block-on-the-human** 原则 skill），但数小时 run 值得一次 checkpoint。

## 阶段 B：设计工作流

分解为原子、可独立 land 的单元。最未知风险优先排序。脚手架与验证先于功能（**foundational-thinking** 原则 skill）。

- 工作前建验证 harness，基线从变更前状态捕获，使检查读作「旧值 vs 新值」。
- 单向门设计决策跑 **architect** skill（它跑 **arena**）。形状已具体的机械工作跳过。对已 settled 设计再跑 arena 是过度工程（**laziness-protocol** 原则 skill）。
- 决定什么扇出。仅跨 seam 并行，各 worker 自有 worktree 或分支（**separate-before-serializing-shared-state** 原则 skill）。不要过度扇出。
- 写下设计的阶段列表。人类 review 的就是这份列表。

然后执行设计。在阶段 C 项之后、阶段 D 之前，把其步骤加为 todolist 具体项。每项在阶段 C 循环纪律下跑，阶段 D 日志贯穿其中，每步 land 一行，而非最后才记整条轨迹。

## 阶段 C：跑循环

每单元是一次实验。陈述假设，做最小变更，对真实 artifact 按谓词度量，推进则保留，否则 revert。应用 **sequence-verifiable-units** 原则 skill：下一单元开始前验证当前单元，不要最后批量检查。

- 通过检查 artifact 验证，不要自报。某物太容易通过时，先怀疑观察方法而非系统。
- 委派工作与 judge 配对。worker  gaming gate 则 reset 并硬化契约。gate 本身错了则在独立变更中修 gate，不要绕路。
- 裁决为 VERIFIED、NOT VERIFIED 或 INCONCLUSIVE。INCONCLUSIVE 不是 pass。不要隐藏 negative。

## 阶段 D：保留审计轨迹

经 **show-me-your-work** skill 记录 run。figure-it-out 的工作通常足够 ambitious，应 commit 轨迹以便 reviewer 在 PR 中阅读。轨迹加 diff 让人回来能信任工作。

## 阶段 E：验证并交回

对照阶段 A 谓词在真实产品上检查整体，不只 harness。把 recurring 纠正编码为 gate、lint 规则、检查或脚本（**encode-lessons-in-structure** 原则 skill）。

**回复：** 设计的 playbook、严谨度及原因、决策轨迹路径、相对谓词已验证什么、仍开放什么。
