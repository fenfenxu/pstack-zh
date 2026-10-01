---
name: typescript-best-practices
description: TypeScript 最佳实践。读取或编辑任意 .ts 或 .tsx 文件时使用。
paths: ["**/*.ts", "**/*.tsx"]
disable-model-invocation: true
---

# TypeScript 最佳实践

先应用 **type-system-discipline** 原则 skill。

| 规则 | 摘要 |
|------|---------|
| 可区分联合 | 用 `kind` 字面量 discriminant 建模变体，使不可能状态无法表示。不要可选字段大杂烩。 |
| 品牌类型 | 用 `& { readonly __brand: "X" }` 标记原始类型，防止混用。边界处校验一次。 |
| 构造式建模 | 构造形状使非法值无法构造。非空用 `[T, ...T[]]`，偶长用 `[T, T][]`，区间用 `start` 加 `duration`。不是运行时 guard，不是 refinement 类型的愿望。 |
| 最简全函数类型 | 对 `T[]` 的一切操作仍全函数时保持 `T[]`。仅当宽松类型迫使 `!`、强转或「不应发生」的 throw 时才加强为 `NonEmpty<T>`。 |
| `unknown` 优于 `any` | 外部数据是 `unknown`。 |
| schema 先于 guard | 手写逐属性 type guard 前，用仓库的运行时 schema 库并从 schema 推断类型，如 `z.infer`。 |
| 禁止 `as` 强转 | 每个 `as` 都是潜在的运行时崩溃。仅在校验后强转。 |
| 收窄层次 | discriminant switch > `in` > `typeof`/`instanceof` > 用户 type guard > `as`。 |
| Type guard | 必须验证声称。撒谎的 guard 比 `as` 更糟，bug 藏在「安全」名字后。命名为 `isX` 或 `hasX`。 |
| 穷尽性 | 在 default 分支内联 `const _exhaustive: never = x;`，新增变体时编译器报错。 |
| `satisfies` 优于 `as` | 校验值而不拓宽字面量类型。 |
| 边界校验 | 数据进入处解析为命名领域类型。`Record<string, unknown>`（无论怎么拼）止于该 parse。内部信任类型。见 **boundary-discipline** 原则 skill。 |
| 由 schema 派生类型 | 优先 `Pick`/`Omit`/`Parameters`/`ReturnType`/`Awaited`/`typeof`，再声明新 interface。 |
| 对象参数 | 传对象而非位置参数，参数顺序自文档化。热路径可跳过（每帧渲染、分词器、解析器）。 |
| 真实测试 | 能跑真代码就不要 mock。优先框架真实测试原语加 leak/disposable 检查，在运行构建中验证 UI。仅 mock 本地跑不了的东西。 |
| 结构化遥测 | 优先结构化 logger 诊断，上下文足以凭 id 调试。上线代码不用 `console.log`。 |

示例见 `references/patterns.md`。
