---
name: principle-encode-lessons-in-structure
description: "当你发现自己第二次写同一条指令，或注意到 recurring correction 时适用。把规则编码为 lint、metadata 标志、运行时检查或脚本，而不是更多文字。"
disable-model-invocation: true
---

# 在结构中编码教训

把 recurring fix 编码进机制（工具、代码、metadata、自动化），而不是文字指令。每个错误、人工纠正和意外结果都是学习信号。捕获它、路由它、闭环它。

**原因：** 文字指令容易被忽略。读者必须注意到、记住并遵守。结构机制（lint 规则、metadata 标志、运行时检查、自动化脚本）无需配合即可强制执行规则。

**模式：**
当你发现自己第二次写同一条指令时：
1. 问：这能否做成 lint 规则、metadata 标志、运行时检查或脚本？
2. 若能，编码它。删除该指令
3. 若不能（需要判断），让指令更醒目，并加上失败模式的示例

**选择最强机制。** 当多种机制都可行时，选当前情况允许的最强者（不可表示的状态无法编译，然后是 lint 或 banned API 在 CI 失败，然后是 canonical helper，最后是运行时检查），因为 agent 会复制周围代码已有的做法，较弱的守卫会成为下一个模板。

**推论：** 若修复是结构性的，只用结构性修复。指令只是症状。

**反馈闭环：**
- **捕获每次纠正。** 当人工介入或测试失败时，判断是一次性还是模式。
- **路由到正确层。** 一次性 -> brain note。recurring fix -> skill 或 lint 规则。系统性问题 -> principle。
- **闭环。** 不要只记录。立即应用或创建具体 todo。

**反模式：**
- 承认但不记录（「我会记住」不会持久）
- 记录但不路由（关于应存在的 lint 规则的 brain note 是浪费，除非 lint 规则被实现）
- 修复但不泛化（修一个实例却留下 recurring pattern）
