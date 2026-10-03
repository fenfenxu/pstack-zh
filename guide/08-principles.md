---
title: "用原则名来转向"
description: "pstack 自带 24 条原则，每条都是单独的 skill。任务中途报出原则名，就能给 agent 改方向，一句话比一大段指示更准。"
sourceUrl: "https://github.com/cursor/plugins/blob/23e4138daa01c42d4969f7a5465f82704e64f798/pstack/docs/guide/08-principles.md"
---

> [!NOTE]
> **来源**
>
> 这是中文译文，方便对照阅读。本站不发行中文版插件。
>
> 对照英文：[本站英文页](https://pstack.ganhai.cloud/en/skills-zh/official-guide/08-principles/)
>
> 英文原文出处：cursor/plugins 仓库 [`pstack/docs/guide/08-principles.md`](https://github.com/cursor/plugins/blob/23e4138daa01c42d4969f7a5465f82704e64f798/pstack/docs/guide/08-principles.md)（提交 `23e4138`）

# 用原则名来转向

pstack 自带 24 条原则，每条都是一个单独的 skill。每个多步任务开始时，`/poteto-mode` 都会读一遍原则索引，用上这个任务触发的那几条，并在回复里点名用到的每条原则，说明它改变了哪个决定。

原则不用你去调用。你用的是它们的名字，拿来给 agent 指方向。每个名字背后都是一条完整的规则，agent 已经读过。所以一个短语改起方向来，比一大段指示还准。

## 转向的实际用法

比如 agent 正打算在三个已有的适配器旁边，再硬塞一个新的：

```text
use subtract before you add. delete the obsolete adapters first, then design what's left.
```

比如它因为构建通过了，就说任务成功：

```text
apply prove it works. run the real import flow and show me the written records.
```

比如两个并行的尝试正要往同一个分支里写：

```text
separate before serializing shared state. give each attempt its own worktree, no locks.
```

这些短语管用，是因为背后的规则很具体。agent 仍然得在回复里说明，这条规则改变了哪个决定。如果引用了原则，背后却没有对应的决定，那就说明它只是报了个名字，并没有真正用上。

## 二十四条，各用一句话

核心原则决定做多少，以及什么时候该重新考虑设计：

- [Laziness Protocol](../skills/principle-laziness-protocol/SKILL.md) 优先删代码，优先选能解决问题的最小改动。
- [Foundational Thinking](../skills/principle-foundational-thinking/SKILL.md) 先定下核心数据结构，再写逻辑。
- [Redesign from First Principles](../skills/principle-redesign-from-first-principles/SKILL.md) 接入新需求时，就当它从第一天起就在。
- [Attack the Premise](../skills/principle-attack-the-premise/SKILL.md) 两次以上的修复都失败时，先清点失衡落在哪些参与方身上，再质疑这些修复共有的前提。
- [Subtract Before You Add](../skills/principle-subtract-before-you-add/SKILL.md) 先减掉累赘，再往上加东西。
- [Minimize Reader Load](../skills/principle-minimize-reader-load/SKILL.md) 压平读者得记在脑子里的那些层次和隐藏状态。
- [Outcome-Oriented Execution](../skills/principle-outcome-oriented-execution/SKILL.md) 重写时直奔目标设计，不去保留用完就扔的兼容状态。
- [Experience First](../skills/principle-experience-first/SKILL.md) 用户得到的结果优先，实现上的方便靠后。
- [Exhaust the Design Space](../skills/principle-exhaust-the-design-space/SKILL.md) 没有先例可循时，做两三个互相竞争的原型。
- [Build the Lever](../skills/principle-build-the-lever/SKILL.md) 写出能完成或证明这项工作的脚本，让审查者能重跑。

架构原则决定状态、校验和兼容逻辑放在哪里：

- [Model the Domain](../skills/principle-model-the-domain/SKILL.md) 把重复出现的规则收进一个结构里，而不是散成一堆条件判断。
- [Boundary Discipline](../skills/principle-boundary-discipline/SKILL.md) 在边界上校验，内部类型就直接信任。
- [Type System Discipline](../skills/principle-type-system-discipline/SKILL.md) 让非法状态在类型里根本表示不出来。
- [Make Operations Idempotent](../skills/principle-make-operations-idempotent/SKILL.md) 不管重试多少次，都落到同一个最终状态。
- [Migrate Callers Then Delete Legacy APIs](../skills/principle-migrate-callers-then-delete-legacy-apis/SKILL.md) 迁移调用方和删除旧 API 在同一波里完成。
- [Separate Before Serializing Shared State](../skills/principle-separate-before-serializing-shared-state/SKILL.md) 先去掉共享，再考虑加协调。

验证原则规定什么才算证明：

- [Prove It Works](../skills/principle-prove-it-works/SKILL.md) 验证真实产物，不拿替代品充数。
- [Fix Root Causes](../skills/principle-fix-root-causes/SKILL.md) 改代码之前，先复现问题，追到根因。
- [Sequence Work into Verifiable Units](../skills/principle-sequence-verifiable-units/SKILL.md) 每个小单元都以一次检查收尾，然后才开始下一个。
- [Test Behavior, Not Implementation](../skills/principle-test-behavior-not-implementation/SKILL.md) 像使用者那样调用代码，断言一个写死的期望值。如果所有导入的函数都返回 `undefined`，测试照样能过，就删掉这个测试。
- [Explain the Number](../skills/principle-explain-the-number/SKILL.md) 测出一个数字后，先说清是什么在限制它，再排除它其实测到了别的东西，然后才轮到有人相信或汇报它。

委派原则让并行工作不至于乱套：

- [Guard the Context Window](../skills/principle-guard-the-context-window/SKILL.md) 大量阅读交给子代理，主对话里只留 finding（读完得出的结论）。
- [Never Block on the Human](../skills/principle-never-block-on-the-human/SKILL.md) 可以撤回的工作就直接往下做，做完把结果交出来。

还有一条元原则：

- [Encode Lessons in Structure](../skills/principle-encode-lessons-in-structure/SKILL.md) 同一条建议你说过两遍，就把它变成 lint、检查或脚本。

这份列表不用背。现在扫一眼就行。等哪天你发现 agent 在做的事，本来报一个这里的名字就能拦住，再回来看。这些名字就是这么记牢的。

下一篇：[把它变成你的](./09-make-it-yours.md)。
