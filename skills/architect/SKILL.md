---
name: architect
description: "在写代码前先勾勒类型、签名与模块结构，并在实现填充过程中持续参与。用于 /architect、「architect this」「design this」，或任何直接开写会锁死错误形态的复杂工作。"
disable-model-invocation: true
---

# Architect

先设计，再实现。用 `not implemented` 函数体与伪代码勾勒类型、函数签名、类形态与模块边界。综合多个模型视角后，按选定草图填充代码。若实现证明草图有误，丢弃草图并重新设计。

## 开始

启动前先打开 todolist，每个阶段一条。

1. 摸底
2. 草图
3. 确认
4. 实现
5. 废弃

## 阶段 A：摸清问题

对新代码会触及的每个系统建立真实心智模型。对相关子系统运行 **how** skill。

只点名文件不算摸底。产出 **how** 所要求的追溯模型。若设计会重定义所有权或分层，还要对现有形态运行 **why** skill，使理由成为约束而非猜测。

仅当工作确实是无需与周边系统集成的纯绿地项目时，才跳过阶段 A。

## 阶段 B：草图

用 **arena** skill 运行设计草图任务，并传入阶段 A 的摸底产物。将 `references/runner-prompt.md` 作为每个 runner 的 prompt。每个候选按 `references/rationale-template.md` 的形态产出设计包。

runner 取自 `pstack-models.mdc` 规则中的 `architect runners` 行，替代 `arena runners` 行。若规则或该行缺失，使用 `claude-opus-5-5-max`、`gpt-5.6-sol-max`、`grok-4.7-xhigh-fast`。别名与被拒条目遵循 **arena** skill 阶段 A 的 runner 规则。

至少设计两次。即使第一个方案看起来够用，综合前也要求至少两个结构不同的候选。这是 **exhaust-the-design-space** 原则 skill 的具体化。要是整体形态上的备选，而非在同一形态里做点状修补。

综合前，用 [`references/design-red-flags.md`](references/design-red-flags.md) 筛查每个候选。拒绝或修订浅模块、信息泄漏、按时间分解与透传方法。

在可行候选之间比较接口深度。优先选择用更小、更简单的公开面隐藏更多复杂度的设计。丰富的接口可通过集中能力而非层层分散来缩短调用链。

Arena 返回一个综合设计包。综合决策填入 rationale 的「综合决策」一节。

## 阶段 C：确认（可选）

默认：直接用综合设计进入实现，无需人工卡点。

仅在调用者明确要求时启用卡点：「/architect with checkpoint」「实现前先给我看」等。此时展示综合设计并等待签字。

无论哪种方式，综合结果都可单独提交一次，作为 **foundational-thinking** 原则 skill 的「先搭脚手架」模式。填充过程中的计划内、有范围的破坏是可以接受的，见 **outcome-oriented-execution** 原则 skill。若要在实现前对设计施加对抗性压力，对综合草图运行 **interrogate** skill。

若人类对形态有异议（卡点时或事后），将其视为阶段 A 的证据。在继续写代码前重新摸底并重跑阶段 B。

## 阶段 D：按草图实现

用代码替换 `not implemented` 函数体，用逻辑替换伪代码。综合草图即契约。

偏离草图是应主动说明的信号，而非应默默吞下的摩擦。若某函数需要草图未预料的参数，要问：是草图错了、需求漏了，还是实现越界了。

## 阶段 E：架构不对就扔掉

若实现持续产生草图无法吸收的摩擦，丢弃草图。不要在错误设计上打补丁，见 **redesign-from-first-principles** 与 **fix-root-causes** 原则 skill。

信号是*模式*，不是单次偶发。典型征兆：

- 同一形态的 workaround 在多处无关代码中反复出现。
- 多个无关边界情况都需要特殊分支。
- 类型需要逃生口（`any`、强转、实践中总被赋值的可选字段）才能编译。
- 草图说状态不共享，却出现「我们需要锁」的条件反射。
- 调用方必须了解抽象内部规则才能使用。
- 实现中出现两次及以上独立、同形的阶段 D 偏离。

用判断力。几个边界情况不足以否定架构。有些问题确实复杂。数据里的复杂度不等于设计里的复杂度。

废弃时：

1. 对已构建内容重新运行 **how** skill。
2. 按 redesign-from-first-principles，仿佛新约束从一开始就是前提，重新设计。
3. 按 **subtract-before-you-add** 原则 skill，先减后加。新草图在扩展前应比旧草图更小。
4. 回到阶段 B，重跑 arena。

## 产出

先写调用方的用法，再从中推导类型草图。小改动：一个含新类型与签名的文件。大工作：模块图加类型定义。Rationale 一并交付，按 `references/rationale-template.md` 组织，含用法草图与综合决策。
