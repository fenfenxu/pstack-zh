---
name: principle-type-system-discipline
description: "设计类型、审查函数签名或在任意静态类型语言中写代码时应用。使非法状态无法表示，标记语义原始类型，在边界解析外部数据，拒绝欺骗编译器，穷尽变体，从权威 schema 派生。"
disable-model-invocation: true
---

# 类型系统纪律

类型检查器是证明助手。用它消除不可能状态、错配原始类型与未处理变体。类型让你忽略的分支，会变成编译器本可阻止的运行时失败。优先把错误与特例定义到不存在，而非增殖 handler。无法表示的状态、全函数与接口 redesign（下文模式）是工具。

适用于任意 typed 语言。`typescript-best-practices` 等 skill 将其落地到具体语法。

**模式：**

- **使非法状态无法表示。** 变体建模为 sum type：TypeScript 可区分联合、Rust/Swift/Kotlin 带 payload 的 enum、Scala sealed class、Haskell/OCaml ADT。不要把状态建模为 contradictory 组合仍可编译的可选字段袋。 subtle 反模式：`{ completed: boolean; completedAt?: Date }` 允许 `completed: true; completedAt: undefined`，无意义。从单一来源如 `completedAt !== null` 推导 boolean，或显式建模变体 `{ kind: 'open' } | { kind: 'done'; at: Date }`。若 bug 迫使你问「这组合真能发生吗？」，类型太松。
- **类型是构造，不是限制。** 从想要的值构造类型，而非从更松类型 carve 再检查。似乎需要 refinement 的 invariant 通常是构造一步之遥。非空列表是 head 加 rest，不是带 length 检查的 list。有效时间区间是 start 加 duration，不是须保持有序的两个 timestamp。无 privileged 表示。pair 列表若如此解释就是偶长 list，选无法构造非法值的形状，再暴露调用方需要的接口。
- **标记语义原始类型。** `UserId` 与 `OrderId` 底层是 string 但不可互换。Rust newtype、Swift opaque type、Kotlin value class、Haskell phantom type、TypeScript branded intersection。创建时校验一次，下游信任类型。
- **外部数据在 parse 前无类型。** RPC payload、JSON、IPC 消息、CLI 参数、配置文件、环境变量、数据库行。每个边界有 parse 函数，把非结构化输入变成 typed 模型。见 **boundary-discipline** 原则 skill 的校验位置。
- **不要欺骗类型系统。** 强转、unsafe coercion、绕过编译器的 assertion function 是 latent 运行时崩溃。编译器证不出就证明它（校验、收窄、细化模型），或接受强转是 hazard。
- **穷尽匹配是编译器的工作。** 对 sum type 匹配时，新增变体未处理须编译失败。用各语言 idiom：TypeScript `never` 绑定、Rust 无注解 `match`、Haskell `-Wincomplete-patterns`、Kotlin sealed class 穷尽性。
- **从权威 schema 派生类型。** proto、OpenAPI、GraphQL schema、数据库 migration 或 design-system token 文件定义形状时，从中派生，不要手写平行类型。见 **encode-lessons-in-structure** 原则 skill。
- **仅在出现 partiality 处加强类型。** 运行时断言、null 检查或「不应发生」的 throw 标记类型太弱之处。把检查上推入类型。然后停。类型系统的工作是跟踪各 use site 须处理的 case，不是尽可能精确描述数据。优先全函数。空 list 的 `sum` 是 0，故取 plain list。空 list 的 `head` 无答案，故要求 non-empty。若否则会 panic，才加强；若无则保持 plain type。

**自检：**

- 「能否写注释说明这组字段何时有效？」若能，类型太松。拆成 sum type。
- 「两个参数同 primitive 类型但含义不同？」标记它们。
- 「这个 `any`、`as`、`assertNotNull` 从哪来？」追到边界并在那里校验。
- 「下月加新变体，编译器会告诉下一个 agent 在哪加 case 吗？」若否，匹配未穷尽。
- 「这类型是否在重复另一文件拥有的形状？」派生，不要重复。
- 「加强类型是为保持操作全函数，还是仅为更精确？」若无否则会 panic，保持 plain type。
