---
name: principle-laziness-protocol
description: "在重构、评估 diff 规模，或想要增加抽象、层级或信号穿线时适用。偏向删除，以及能解决问题的最小变更。"
disable-model-invocation: true
---

# 懒惰协议

- **合并决策。** 不要在多处重复同一选择。放在单一事实来源，再把结果当简单标志传递。
- **最小化 diff。** 只做能解决问题的最小变更。更少行数胜过「优雅」样板。
