---
name: principle-type-system-discipline
description: "设计类型、审查函数签名或在任意静态类型语言中写代码时应用。使非法状态无法表示，标记语义原始类型，在边界解析外部数据，穷尽变体，从权威 schema 派生。"
disable-model-invocation: true
---

# 类型系统纪律

让编译器拒绝不可能的状态、对不上的原始类型，以及没处理的变体。优先把错误和特例定义成不存在，而不是不断加处理分支。TypeScript 的具体写法见 `typescript-best-practices` skill。

- **给语义不同的原始类型各自的类型。** `UserId` 和 `OrderId` 底下都是字符串，但不该能互换。各自给一个类型，比如 TypeScript 里的 branded intersection，或者其他语言里的 newtype。创建时校验一次，之后信任这个类型。如果两个参数是同一种原始类型、意思却不同，就给它们各自的类型。
- **外部数据在解析之前没有类型。** RPC 载荷、JSON、IPC 消息、CLI 参数、配置文件、环境变量、数据库行。每个边界都要有一个解析函数，把没有结构的输入变成带类型的模型。校验放在哪，看 **boundary-discipline** 原则 skill。
- **类型从权威的 schema 派生。** protocol buffer、OpenAPI spec、GraphQL schema、数据库 migration、design-token 文件，或其他文件已经拥有这个形状时，从它派生，不要手写一份平行的类型。见 **encode-lessons-in-structure** 原则 skill。
