---
name: principle-foundational-thinking
description: "写逻辑前适用：选择核心类型与数据结构、编排 scaffold 与 feature 的顺序、问并发 actor 共享什么。先把数据结构做对，下游代码自然清晰。"
disable-model-invocation: true
---

# 基础思维

**结构性决策**保护 option value。**代码级决策**保护 simplicity。

**数据结构优先。** 写逻辑前先定好数据形状。尽早定义核心类型，追踪每个访问模式，选择与 dominant path 匹配的结构。

在代码层面，DRY 结构，不是每一行。类型和数据模型应收敛。三句相似语句仍优于过早抽象。显式优于 clever。测试行为与边界情况，不是行数。

**并发推论。** 在 actor 之间共享 state 前，问「若另一个 actor 并发修改会怎样？」若不是「nothing」，就隔离。

**Scaffold 优先。** 若某物帮助后续每个阶段，先做它。问「后续每个阶段是否都受益于它已存在？」CI、linting、测试基础设施和共享类型是 scaffold。为 option value 排序：setup 在 feature 前，测试在修复前。保持 commit 小且单一目的。

每个增量应落地连贯抽象，或深化已有抽象。不要把新能力以 special-case coordination 散播到各 caller。

减法在 scaffold 之前。先删 dead code，再铺基础。
