---
name: principle-build-the-lever
description: "适用于任何非平凡工作，不限于批量任务：编辑、迁移、分析、检查。构建执行或证明它的工具（codemod、脚本、生成器，或子代理遵循的 skill），而不是手工操作。该工具是审查者可以重复运行的产物。"
disable-model-invocation: true
---
# 构建杠杆

当工作并非琐碎时，构建执行它的工具，而不是手工完成。

**原因：** 两个收益。吞吐：codemod、生成器或脚本每次以相同方式执行，且可免费重跑。信心：该工具是单一产物，审查者可以阅读并重跑以核查工作。手工改动只能重做来再验证。确定性脚本把「相信我」变成「运行这个」。

**模式：** 默认构建杠杆。仅在任务琐碎、或只有几处一眼就能看懂的明显编辑时跳过。

- 先手工完成第一个单元以学习配方，再构建工具。通过在该单元上重跑并 diff 手工版本来证明它。让杠杆可安全重跑。
- 编辑用 codemod 或脚本，重复文件用生成器，分析用 dump-to-sqlite 查询，验证用可重跑检查。
- 确定性杠杆优于并行分发。若工具能一次处理所有单元，自己运行它。不要把脚本能做的事并行分发给子代理手工应用。
- 向子代理并行分发工作时，把杠杆写成它们共同读取的 skill：配方、验证契约和 do-not-touch 围栏，合在一个产物里。放在 delegates 写权限之外，避免它们悄悄改契约。
- 应用本原则应产出文件。若你引用了它但 diff 中没有 codemod、脚本、生成器或 delegate skill，你就没有应用它。
- 若工作会跨 session 存活，提交杠杆。

**平衡：** 门槛是琐碎程度，不是重复次数。一次性工作仍值得建杠杆，当杠杆是让工作可核查的关键时。按 [Laziness Protocol](../principle-laziness-protocol/SKILL.md)，构建完成或证明任务的最小脚本，不要建框架。

与 [Encode Lessons in Structure](../principle-encode-lessons-in-structure/SKILL.md) 不同，后者把 recurring instruction 变成持久护栏。本原则是当前工作的吞吐与可审查性。脚本化验证本身见 [Prove It Works](../principle-prove-it-works/SKILL.md)。
