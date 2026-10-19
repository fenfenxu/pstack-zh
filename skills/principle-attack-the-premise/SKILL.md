---
name: principle-attack-the-premise
description: "当两个或多个共享同一前提的修复在同一关卡失败时适用。再写下一个基于该前提的修复前，先写下前提并直接检验。"
disable-model-invocation: true
---

# 攻击前提

当两个或多个共享同一前提的修复在同一关卡失败时，应怀疑前提，而非修复本身。

**原因：** 每个失败的修复，都是反驳它所依据的信念的证据。第三个基于同一前提的修复，会把精力花在证据已经表明不是原因的地方。

**模式：**
- **把前提写下来。** 前提就是每个失败修复都假设的那一句话。
- **在下次修复前检验前提。** 找一种在前提不成立时会得出不同结果的观察，并实际检查。不要只看失败有多大，还要看失败如何分布。按 [Build the Lever](../principle-build-the-lever/SKILL.md) 把这项检查写成可重复运行的脚本。
- **顺着证据走。** 如果证据与前提矛盾，原因就在别处。找出失败之间有哪些前提没有预测到的共同点。如果证据证实了前提，就按 [Fix Root Causes](../principle-fix-root-causes/SKILL.md) 继续问下一个「为什么」。此前的修复也许只处理了后果，没有处理原因。
- **消除这个条件，而不是补偿它**，见 [Laziness Protocol](../principle-laziness-protocol/SKILL.md)。如果证据表明某个部分总是处于不利位置，就改变把它置于这一位置的机制。事后清理的机制会保留原因，并在每次运行时增加工作。

**停止：** 在前提写下并经过检验之前，不要开始下一次修复。

本原则不同于 [Redesign from First Principles](../principle-redesign-from-first-principles/SKILL.md)，后者围绕新需求重建设计。本原则质疑的是当前设计所假设的事实。
