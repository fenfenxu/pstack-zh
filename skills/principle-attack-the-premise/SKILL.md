---
name: principle-attack-the-premise
description: "当两个或多个共享同一前提的修复在同一关卡失败时适用。在下次修复前，先统计哪些 actor 持有失衡，然后质疑前提，而不是再写一个假设该前提成立的修复。"
disable-model-invocation: true
---

# 攻击前提

当两个或多个共享同一前提的修复在同一关卡失败时，应怀疑前提，而非修复本身。

**原因：** 在共享前提下，每一次失败都是关于前提的证据。

**模式：**
- **把前提写下来。** 前提就是每个失败修复都假设的那一句话。
- **在下次修复前先做一次统计。** 按 actor 统计失衡情况。统计展示的是哪些 actor 持有失衡，而非失衡有多大。按 [Build the Lever](../principle-build-the-lever/SKILL.md) 将统计写成可重复运行的脚本。
- **解读偏斜。** 如果每次运行都是同一批 actor 持有大部分失衡，说明有东西在给他们分配该角色。找出分配角色的机制。该分配就是下一个「为什么」，见 [Fix Root Causes](../principle-fix-root-causes/SKILL.md)。
- **消除不对称，而不是补偿它**，见 [Laziness Protocol](../principle-laziness-protocol/SKILL.md)。在 actor 之间轮换角色、随机化分配，或迁移角色，使没有 actor 在每次运行中都持有它。返回路径、共享池、批量交接或定期再平衡，都会保留分配机制，并在每次运行增加额外工作。

**停止：**
- 在前提写下且统计完成之前，不要开始下一次修复。
- 如果统计结果在各 actor 间均匀，前提不是原因。到别处找原因，并保留统计作为证据。

本原则不同于 [Redesign from First Principles](../principle-redesign-from-first-principles/SKILL.md)，后者围绕新需求重建设计。本原则质疑的是当前设计所假设的事实。
