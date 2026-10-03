---
name: no-comments
description: "开出 Comment Sicko，修掉接受的 finding，并为注释里声称的约束提出用代码落实的办法。"
disable-model-invocation: true
---

# No comments

开出 Comment Sicko。按接受的 finding 去做。

以 Comment Sicko 不带成见的眼光为准。

## 范围

用调用方给的文件或 diff。没有的话，就用当前分支相对基础分支（默认 `main`）的 diff，包括工作区里没提交的改动。

## 步骤

1. 用 `Task` 开一个 `subagent_type: "Comment Sicko"` 的子代理。把范围传给它。不要复述它的规则。
2. 检查它的报告和 diff。以下这些要拒掉：改了应用代码、超出范围、删掉了受例外保护的注释、`MUST KILL` 给的理由不对，以及把刻意保留的代码当成罪证的标记。针对我们自己代码里出人意料之处、要求改结构的标记，仍然算要处理，不要把那些注释恢复回来。一条 keep 只有在能证明它说的是我们改不了的东西时才成立。检查范围内有没有漏掉的 lint 和 TypeScript 抑制注释。涉及正确性或安全的抑制注释，仍然是要处理的 `MUST KILL`。只有符合确切的例外、并有范围内的证明时，才恢复被删的注释。在接受理由单薄的 `IMPORTANT` 或 `do not remove` 的 kill 或 keep 之前，对它们涉及的符号跑一遍 `/how` 或 `/why`。kill 拿不准时，不恢复。keep 被驳倒或仍然拿不准时，删掉它。拒掉一份报告后，撤销它的改动，点明哪里不对，重跑一次。第二份再被拒，就报告为未解决，并让 `/no-comments` 失败。
3. 简单的已接受标记直接修：删掉死路径、去掉一个参数，或者改用真正的 API。只要有一处修法需要先定形态，就对接受的这一批标记和周边代码跑一次 `/architect`。停在草图这一步。architect 负责定形态，第 4 步负责实现。
4. 在范围内做最小的根因修复。去掉每一个点了名的 workaround。根因在范围外时，合入范围内最小的修复，其余的报告为未解决。**principle-fix-root-causes** 和 **principle-redesign-from-first-principles** 这两个 skill 只用来指导意图。两者都不允许你扩大范围，也不允许你去修范围外的同类问题。绝不在症状上加防护补丁。
5. 约束类注释写的是 `do not remove`、`do not change wording` 或 `talk to X before changing`。说的是我们改不了的东西的 keep，留着。为其余的约束提出范围内成本最低的落实办法：类型、运行时检查、测试或 CI 里的 lint。交互场景下等用户批准。无人值守和 eval 场景需要调用方事先批准。批准了，就先用代码落实，再删注释。没批准，就删掉注释，把这条约束报告为未解决，并为范围外的工作画个草图。
6. 报告：删了多少条、恢复了哪些注释、重跑了几次、architect 的草图、做了哪些修复、提出了哪些落实办法、落实了哪些、哪些约束没有强制、还有哪些未解决的工作。
