---
name: principle-test-behavior-not-implementation
description: "编写、修改或保留测试时应用。像使用者那样调用代码，断言他们看到的确切结果。若每个导入的函数都返回 undefined 测试仍会通过，就重写或删除。"
disable-model-invocation: true
---

# 测行为，不测实现

测试要像使用者那样调用代码，并断言他们看到的确切结果。如果每个导入的函数都返回 `undefined` 或一个错误的值，测试仍然能通过，就重写或删掉。

下面这些形态过不了这个检查：

- 断言太松，或者没有断言：`toBeDefined`、`toBeTruthy`、`not.toThrow`、`toBeGreaterThan(0)`。
- 只断言调用了或没调用：`toHaveBeenCalled`、`not.toHaveBeenCalled`、`toBeUndefined`、`toEqual([])`。
- 期望值由被测代码算出来：`expect(f(a)).toBe(f(a))`。
- 复述常量、配置默认值、表里的一行，或提示词字符串。
- 断言的是测试自己造的数据，正文里没有调用被测对象。

改法：在测试正文里用一个具体输入调用被测对象，断言字面的输出或效果。对 mock，断言它收到的载荷。对「没有」的情况，还要用另一个输入断言「有」。对常量，用一个输入去测读取它的机制，不要复述那个值。

跨表行的关系检查（一个键在两张表里、父记录存在）和 `*.test-d.ts` 里的类型检查，可以留着。
