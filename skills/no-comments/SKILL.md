---
name: no-comments
description: "Spawn Comment Sicko，修复已接受的发现，并对声称的约束提供编码方案。"
disable-model-invocation: true
---

# No comments

Spawn Comment Sicko。按已接受的发现行动。

以 Comment Sicko 的新鲜视角为准。

## 范围

用调用者的文件或 diff。否则用相对基分支（默认 `main`）的当前 diff，含工作树。

## 步骤

1. 用 `Task` spawn，`subagent_type: "Comment Sicko"`。传入范围。不要复述其规则。
2. 检查其报告与 diff。拒绝应用代码编辑、范围逃逸、受例外保护的删除、错误的 `MUST KILL` 理由，以及把有意保留代码当罪证的 flag。对我们代码的意外处 reshape flag 以保持可行动。不要恢复那些注释。保留仅在有证明涉及我们无法改的东西时成立。审计范围内遗漏的 lint 与 TypeScript suppression。正确性或安全 suppression 仍是可行动的 `MUST KILL`。恢复删除须有精确例外与范围证明。接受薄 `IMPORTANT` 或 `do not remove` 的 kill/keep 前，对其 symbol 跑 `/how` 或 `/why`。kill 模糊则不恢复。keep 被反驳或仍模糊则删除。一次拒绝后 revert 并重跑，点名失败。第二次仍拒绝则报告未解决，使 `/no-comments` 失败。
3. 直接修复琐碎已接受 flag：删死路径、去参数、用真实 API。若任何修复需要形态，对已接受集合及周边代码跑一次 `/architect`。停在草图。Architect 定形态。步骤 4 实现。
4. 在范围内做最小根因修复。移除每个指名 workaround。根因超出范围则 land 最小范围内修复并报告其余未解决。**principle-fix-root-causes** 与 **principle-redesign-from-first-principles** skill 仅指导意图。二者都不授权扩大围栏或修范围外实例。不要打症状 guard 补丁。
5. 约束注释说 `do not remove`、`do not change wording` 或 `talk to X before changing`。保留关于我们无法改之物的 keep。提供范围内最便宜的类型、运行时、测试或 CI lint 编码。等待交互批准。无人值守与 eval 须调用者预批。若批准，编码后删除。否则删除、报告约束未解决并草拟范围外工作。
6. 报告删除数、恢复的注释、重跑、architect 草图、修复、编码 offer、已编码项、未强制约束及其他未解决工作。
