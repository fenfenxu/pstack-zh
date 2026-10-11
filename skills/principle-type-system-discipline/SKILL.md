---
name: principle-type-system-discipline
description: "设计类型、审查函数签名或在任意静态类型语言中写代码时应用。使非法状态无法表示，标记语义原始类型，在边界解析外部数据，拒绝欺骗编译器，穷尽变体，从权威 schema 派生。"
disable-model-invocation: true
---

# 类型系统纪律

让编译器拒绝不可能的状态、对不上的原始类型，以及没处理的变体。优先把错误和特例定义成不存在，而不是不断加处理分支。TypeScript 的具体写法见 `typescript-best-practices` skill。

- **让非法状态无法表示。** 变体用 sum type 来建模（可区分联合、带 payload 的 enum、sealed class），不要用一袋可选字段，让互相矛盾的组合也能编译。`{ completed: boolean; completedAt?: Date }` 允许 `completed: true` 却没有日期。从 `completedAt` 推导这个布尔值，或者建成 `{ kind: 'open' } | { kind: 'done'; at: Date }`。如果你能写一条注释说明这组字段什么时候才有效，就把它拆成 sum type。
- **类型是构造出来的，不是限制出来的。** 从你想要的值把类型搭起来，不要从一个更松的类型里用检查去抠。非空列表是一个头加上剩下的部分。有效的时间区间是起点加上时长。选一个造不出非法值的形状，再在上面露出调用方需要的接口。
- **给语义不同的原始类型各自的类型。** `UserId` 和 `OrderId` 底下都是字符串，但不该能互换。各自给一个类型，比如 TypeScript 里的 branded intersection，或者其他语言里的 newtype。创建时校验一次，之后信任这个类型。如果两个参数是同一种原始类型、意思却不同，就给它们各自的类型。
- **外部数据在解析之前没有类型。** RPC 载荷、JSON、IPC 消息、CLI 参数、配置文件、环境变量、数据库行。每个边界都要有一个解析函数，把没有结构的输入变成带类型的模型。校验放在哪，看 **boundary-discipline** 原则 skill。
- **不要骗类型系统。** 强制转换、不安全的强制，以及绕过编译器的断言函数，都是潜伏的运行时崩溃。编译器证不了的事实，要么你自己证明（校验、收窄、把模型改细），要么承认这次转换是隐患。每个 `any`、`as` 或 `assertNotNull` 都追到边界，在那里校验。
- **穷尽匹配是编译器的事。** 对 sum type 做匹配时，新增变体却没处理，必须编译失败，这样编译器会告诉下一个 agent 该在哪加分支。用你这门语言的写法：TypeScript 里绑一个 `never` 类型，Rust 里不写注解的 `match`，Haskell 的 `-Wincomplete-patterns`，Kotlin 里 sealed class 的穷尽匹配。
- **类型从权威的 schema 派生。** protocol buffer、OpenAPI spec、GraphQL schema、数据库 migration、design-token 文件，或其他文件已经拥有这个形状时，从它派生，不要手写一份平行的类型。见 **encode-lessons-in-structure** 原则 skill。
- **只在出现做不到的地方收紧类型。** 运行时断言、null 检查，或一句「这不该发生」的 throw，标出类型太松的地方。把这个检查推进类型里。然后停。类型系统要跟踪的是每个使用点必须处理的情况，不是把数据描述得越精确越好。优先全函数。空列表的 `sum` 是 0，所以它接受普通列表。空列表的 `head` 没有答案，所以它要求非空列表。如果没有这个收紧也不会 panic，就保持普通类型。
