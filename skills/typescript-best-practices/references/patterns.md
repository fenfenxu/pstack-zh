# TypeScript 模式

`SKILL.md` 各规则的代码示例。底层原则与语言无关。见 **type-system-discipline** 与 **boundary-discipline** 原则 skill。

## 品牌类型

标记原始类型以防混用。边界处校验一次。下游代码信任该类型。

```ts
type AgentId = string & { readonly __brand: "AgentId" };

function parseAgentId(input: string): AgentId {
  if (!isUUID(input)) throw new Error(`Invalid agent id: ${input}`);
  return input as AgentId;
}

function focusAgent(id: AgentId): void {
  /* input is trusted */
}
```

匹配 `readonly __brand: 'X'` 形态。不要发明新约定。

## 可区分联合

用字面量 discriminant 建模变体。各变体共享字段名且取值唯一，不可能组合无法表示。

```ts
// Don't. Boolean + optionals lets contradictory states exist.
type DiffState = { loading: boolean; diff?: GitDiff; error?: string };

// Do. Only valid states exist.
type DiffState =
  | { kind: "loading" }
  | { kind: "ready"; diff: GitDiff }
  | { kind: "error"; error: string };
```

选一个 discriminant 名（`kind`、`type`、`tag`）并保持一致。

## 构造式建模

从合法部分构造类型，而非用运行时检查限制宽松类型。

非空，通过可变元组：

```ts
type NonEmpty<T> = [T, ...T[]];

// Don't: T[] plus a length check every caller must repeat
function pickWinner(entries: string[]): string {
  if (entries.length === 0) throw new Error("no entries");
  return entries[Math.floor(Math.random() * entries.length)];
}

// Do: an empty value of the type can't exist
function pickWinner(entries: NonEmpty<string>): string {
  return entries[Math.floor(Math.random() * entries.length)];
}
```

plain `T[]` 到达处，用 guard 收窄一次。事实随后留在类型中：

```ts
const isNonEmpty = <T>(arr: T[]): arr is NonEmpty<T> => arr.length > 0;
```

偶长，作为 pair：

```ts
type Pairs<T> = [T, T][];
```

时间区间，作为 start 加 duration：

```ts
// Don't: a comment holds the invariant
type TimeRange = { start: Date; end: Date }; // start <= end

// Do: a negative range can't be written; derive end when needed
type TimeRange = { start: Date; durationMs: number };
```

保持 `durationMs` 为 plain number。仅当 raw number 可能被传在 duration 位置时才 brand（见品牌类型），不要 reflex。选使坏状态无法构造的表示，再在其上暴露所需读法（`pairs.flat()`、`rangeEnd()` helper）。

## 最简全函数类型

不要处处加强。对 `T[]` 的一切操作仍全函数时保持 `T[]`：

```ts
const sum = (xs: number[]) => xs.reduce((a, b) => a + b, 0); // [] is 0, fine
```

宽松类型在 use site 迫使撒谎时加强。信号是 `!`、`arr[0] as T` 与「不应发生」的 throw：

```ts
// Don't: partiality smuggled past the compiler
function newestSession(sessions: Session[]): Session {
  return sessions.at(0)!;
}

// Do: strengthen the input; the assertion disappears
function newestSession(sessions: NonEmpty<Session>): Session {
  return sessions[0];
}
```

把结果弱化为 `Session | undefined` 是另一种全函数签名。

## `unknown` 优于 `any`

外部数据始终是 `unknown`。使用前收窄。

```ts
// Don't
function handle(input: any) {
  return input.foo.bar;
}

// Do
function handle(input: unknown) {
  if (typeof input === "object" && input !== null && "foo" in input) {
    // narrowed; compiler verifies access
  }
}
```

外部来源包括 RPC payload、`JSON.parse`、`postMessage`、IPC、文件内容、环境变量、数据库结果。

## schema 先于手写 guard

为外部数据手写逐属性 type guard 前，找仓库的运行时 schema 库与现有 schema。让一个 schema 拥有校验并从其派生 TypeScript 类型。不要同时维护 schema、重复 interface 与可能 drift 的 guard。

```ts
import { z } from "zod";

const UserSchema = z.object({
  id: z.string().uuid(),
  role: z.enum(["admin", "member"]),
});

type User = z.infer<typeof UserSchema>;

function parseUser(input: unknown): User {
  return UserSchema.parse(input);
}
```

failure 是预期分支时用 `safeParse`。仓库用其他 schema 库时用等价推断 helper。不要为一个 guard 加新 schema 依赖。本规则优先代码库已信任的 schema 系统。

## 禁止 `as` 强转

每个 `as` 都是潜在的运行时崩溃。仅在类型系统已验证声称后强转。

```ts
import { z } from "zod";

// Don't
const user = data as User;

// Don't
function isUser(data: unknown): data is User {
  return typeof data === "object" && data !== null && "id" in data;
}

// Do
const userSchema = z.object({ id: z.string(), name: z.string() });
type User = z.infer<typeof userSchema>;

function parseUser(data: unknown): User {
  return userSchema.parse(data);
}
```

类型先写下来时，给校验器标注它要证明的那个类型。编译器就会拒绝一个证明得比这个类型少的校验器。把下面对象里的 `name` 删掉，赋值就编译不过。

```ts
type User = { id: string; name: string };

const userSchema: z.ZodType<User> = z.object({ id: z.string(), name: z.string() });
```

从现有代码 refactor 掉 `as` 时，找出 TypeScript 无法推断的原因：

- 缺 discriminant：加一个，改用可区分联合。
- 源类型过宽（如 `Record<string, unknown>`）：收窄。
- 无类型边界：用拥有这个结构的 schema 来解析。没有 schema 时才加一个。
- 确实无法表达：用 branded type 或 `satisfies`。

## 收窄层次

从最好到最后手段：

1. **可区分联合 switch / if。** 编译器自动收窄。
2. **`in` 运算符。** `"key" in obj` 收窄到含该 key 的变体。
3. **`typeof` / `instanceof`.** 用于原始类型与类实例。
4. **用户 type guard。** 上述不够时。
5. **`as` 强转。** 仅在校验后。

```ts
function area(s: Shape): number {
  if ("radius" in s) return Math.PI * s.radius ** 2; // narrowed to circle
  return s.width * s.height; // narrowed to rect
}
```

## Type guard

guard 必须真正验证声称。撒谎的 guard 比 `as` 更糟。

```ts
function isCircle(s: Shape): s is Shape & { kind: "circle" } {
  return s.kind === "circle";
}
```

可能时优先 discriminant 收窄。

## 穷尽性

在 default 分支把 discriminant 赋给 `never` 类型局部变量。

```ts
// Value-returning switch
function area(s: Shape): number {
  switch (s.kind) {
    case "circle":
      return Math.PI * s.radius ** 2;
    case "rect":
      return s.width * s.height;
    default: {
      const _exhaustive: never = s;
      return _exhaustive;
    }
  }
}

// Void switch
function handle(s: Shape): void {
  switch (s.kind) {
    case "circle":
      drawCircle(s);
      break;
    case "rect":
      drawRect(s);
      break;
    default: {
      const _exhaustive: never = s;
      void _exhaustive;
    }
  }
}
```

有返回值的 switch 用 return 风格，语句 switch 用 void 风格。

## `satisfies` 优于 `as`

`satisfies` 校验而不拓宽字面量类型。

```ts
// Don't. Widens, loses literal types.
const config = { theme: "dark", cols: 3 } as Config;

// Do. Validates AND preserves literal types.
const config = { theme: "dark", cols: 3 } satisfies Config;
// config.theme is "dark" (literal), not string
```

## 边界校验

数据进入处校验一次。内部信任类型。见 **boundary-discipline** 原则 skill。

- **Wire 格式**（proto、JSON-RPC）：用 `ignoreUnknownFields` 解析，使前向兼容变更不破坏旧客户端。
- **持久化 JSON：** 带版本 blob，parse 外包 try/catch。
- **不要在调用链深处重复校验。**

## 由 schema 派生的类型

当 `.proto`、OpenAPI spec、GraphQL schema 或数据库 migration 已定义形状时，从生成类型派生，不要重复。

```ts
// Don't. Duplicate shape, drifts when the schema changes.
type CheckSummary = {
  totalCount: number;
  checks: { name: string; status: string }[];
};
function renderChecks(s: CheckSummary) {
  /* ... */
}

// Do. Derive from the generated schema type.
import type { ChecksMessage } from "<generated module>";
function renderChecks(s: Pick<ChecksMessage, "totalCount" | "checks">) {
  /* ... */
}
```

写新 interface 前先试 `Pick`、`Omit`、`Parameters`、`ReturnType`、`Awaited`、`typeof`。

## 对象参数

```ts
// Don't. Swap two args, still compiles.
openFile(uri, {
  startLineNumber: 10,
  startColumn: 1,
  endLineNumber: 10,
  endColumn: 1,
});

// Do. Order-independent, self-documenting.
openFile({
  uri,
  selection: {
    startLineNumber: 10,
    startColumn: 1,
    endLineNumber: 10,
    endColumn: 1,
  },
});
```

热路径可跳过：每帧渲染、分词器、解析器，任何 tight loop 中分配成本重要的场景。
