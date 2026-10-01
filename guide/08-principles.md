---
title: "用原则名来转向"
description: "pstack 有 23 条原则 skill。用它们的名字把工作转向，一个短语比一段指示更准。"
sourceUrl: "https://github.com/cursor/plugins/blob/12d587dfb20741cafc376c42c696c5f6e2a64487/pstack/docs/guide/08-principles.md"
---

> [!NOTE]
> **来源**
>
> 这是中文译文，方便对照阅读。本站不发行中文版插件。英文原文 [`pstack/docs/guide/08-principles.md`](https://github.com/cursor/plugins/blob/12d587dfb20741cafc376c42c696c5f6e2a64487/pstack/docs/guide/08-principles.md)，取自 pstack 官方仓库 cursor/plugins 的提交 `12d587d`。
>
> [本站英文页](https://pstack.ganhai.cloud/en/skills-zh/official-guide/08-principles/)

# 用原则名来转向

pstack 把 23 条原则作为单独的 skill 带上。`/poteto-mode` 在每个多步任务开始时读它们的索引，应用任务触发的那些，并在回复里点名每一条用过的原则，以及它改变的那个决定。

你不调用原则。你用它们的名字来转向。每个名字指向代理已经读过的一条完整 rule。所以一个短语比一段指示更能精确地改道。

## 转向的实际用法

假如代理正要把一个新适配器栓到三个已有适配器上：

```text
use subtract before you add. delete the obsolete adapters first, then design what's left.
```

假如它因为构建通过就声称成功：

```text
apply prove it works. run the real import flow and show me the written records.
```

假如两个并行尝试正要写到同一个分支：

```text
separate before serializing shared state. give each attempt its own worktree, no locks.
```

每个短语能落地，是因为背后的 rule 很具体。代理仍须在回复里说明，这条 rule 改变了哪个决定。只引用原则、后面没有决定，说明它在点名，没有真正应用。

## 二十三条，各用一句话

核心原则决定做多少，以及何时重新想设计：

- [Laziness Protocol](../skills/principle-laziness-protocol/SKILL.md) 优先删除，以及能解决问题的最小改动。
- [Foundational Thinking](../skills/principle-foundational-thinking/SKILL.md) 先选定核心数据结构，再写逻辑。
- [Redesign from First Principles](../skills/principle-redesign-from-first-principles/SKILL.md) 把新需求当成从第一天就在那里，再把它整合进来。
- [Attack the Premise](../skills/principle-attack-the-premise/SKILL.md) 先普查哪些行动者持有不平衡，再质疑两个或更多失败修复所共享的前提。
- [Subtract Before You Add](../skills/principle-subtract-before-you-add/SKILL.md) 先去掉死重，再在上面建造。
- [Minimize Reader Load](../skills/principle-minimize-reader-load/SKILL.md) 折叠读者必须记在脑子里的层和隐藏状态。
- [Outcome-Oriented Execution](../skills/principle-outcome-oriented-execution/SKILL.md) 让重写收敛到目标设计，不保留用完即弃的兼容状态。
- [Experience First](../skills/principle-experience-first/SKILL.md) 选择用户得到的结果，不选实现上的方便。
- [Exhaust the Design Space](../skills/principle-exhaust-the-design-space/SKILL.md) 没有先例时，做两三个互相竞争的原型。
- [Build the Lever](../skills/principle-build-the-lever/SKILL.md) 做出能完成工作或证明工作的脚本，让审查者可以重跑。

架构原则决定状态、校验和兼容放在哪里：

- [Model the Domain](../skills/principle-model-the-domain/SKILL.md) 把重复的 rule 编进一个结构，不散落成条件判断。
- [Boundary Discipline](../skills/principle-boundary-discipline/SKILL.md) 在边界上校验，并信任内部类型。
- [Type System Discipline](../skills/principle-type-system-discipline/SKILL.md) 让非法状态无法被表示。
- [Make Operations Idempotent](../skills/principle-make-operations-idempotent/SKILL.md) 让重试收敛到同一个终态。
- [Migrate Callers Then Delete Legacy APIs](../skills/principle-migrate-callers-then-delete-legacy-apis/SKILL.md) 在同一波里迁移并删除。
- [Separate Before Serializing Shared State](../skills/principle-separate-before-serializing-shared-state/SKILL.md) 先去掉共享，再加协调。

验证原则定义什么算证明：

- [Prove It Works](../skills/principle-prove-it-works/SKILL.md) 验证真实产物，不验证替身。
- [Fix Root Causes](../skills/principle-fix-root-causes/SKILL.md) 改代码之前先复现，并追溯到根因。
- [Sequence Work into Verifiable Units](../skills/principle-sequence-verifiable-units/SKILL.md) 每个小单元以一次检查结束，再开始下一个。
- [Test Behavior, Not Implementation](../skills/principle-test-behavior-not-implementation/SKILL.md) 按用户的方式调用代码，并对一个字面期望值做断言。如果每个导入的函数都返回 `undefined`，测试仍会通过，就删掉这个测试。

委派原则让并行工作保持清醒：

- [Guard the Context Window](../skills/principle-guard-the-context-window/SKILL.md) 把大量阅读路由给子代理，把 finding 留在主对话里。
- [Never Block on the Human](../skills/principle-never-block-on-the-human/SKILL.md) 在可逆的工作上继续前进，并交出结果。

还有一条元原则：

- [Encode Lessons in Structure](../skills/principle-encode-lessons-in-structure/SKILL.md) 把你重复过两次的建议变成 lint、检查或脚本。

别背这份列表。现在扫一遍。等你抓住代理在做某件这里的名字本可以阻止的事，再回来。词汇就是这样留下的。

下一篇：[把它变成你的](./09-make-it-yours.md)。
