---
name: principle-test-behavior-not-implementation
description: "编写、修改或保留测试时应用。像用户一样调用代码，对字面预期值断言他们观察到的结果。若每个 import 的函数都返回 undefined 测试仍会通过，则重写断言或删除测试。"
disable-model-invocation: true
---

# 测行为，不测实现

测试像用户一样调用代码，对字面预期值断言他们观察到的结果。断言代码做了哪些调用，或复述代码所含常量的测试，二者都不是。

检查：保留测试前问，若每个 import 的函数都返回 `undefined`，测试是否仍通过。若是，它未观察任何行为，无法因缺陷失败。重写断言或删测试。

**原因：** 无法因缺陷失败的测试浪费 CI 与 review，却什么也抓不到。把常量写死在断言里，还会在有人改常量或它复述的 prompt 时失败，从而阻止该编辑。

**每个 import 都返回 `undefined` 仍会通过的五类形态：**

- **弱断言或无断言。** 无 `expect`，或只有 `toBeDefined`、`toBeTruthy`、`not.toThrow`、`toBeInstanceOf`、`toBeGreaterThan(0)`。
- **仅 mock 或 absence。** 只有 `toHaveBeenCalled`、`not.toHaveBeenCalled`、`toBeUndefined`、`toEqual([])`、`toHaveLength(0)`、`not.toBe(wrongValue)`。
- **自指。** 预期值来自被测代码：`expect(f(a)).toBe(f(a))`、`expect(parsed.url).toBe(buildUrl(...))`。
- **把常量写死。** 断言复述手维护常量、配置默认、表行或 prompt 字符串：`expect(LIMITS.maxTools).toBe(8)`、`expect(PROMPT).toContain("You are")`。
- **fixture 断言 fixture。** 断言读测试构建的数据或 `beforeEach` 算出的值，主体从未在 body 内运行。

**修复：** 在测试 body 内用具体输入调用主体，断言字面输出或可观察效果，`expect(slugify("Hello, World!")).toBe("hello-world")`。对 absence，在同一测试中对另一输入断言 presence。对常量，用读它的机制测一个输入，而非复述值。对 mock，断言收到的 payload 或调用后状态，不是被调用。若无此类断言，删测试。

**保留** 跨表行的关系测试（键在两表都存在、父存在），以及 `*.test-d.ts` 中的编译期检查。
