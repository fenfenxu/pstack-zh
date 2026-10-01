---
name: blast-radius
description: "在变更上线前找出它可能在别处破坏什么，超出 diff 范围，并通过运行真实代码而非写报告来证明其安全所依赖的那一条事实。用于「blast radius of X」「what could this break」，或审查你不信任的小 diff。"
disable-model-invocation: true
---

# 爆炸半径

在变更上线前找出它在别处会破坏什么。用于「blast radius of X」「what could this break」，或审查你尚不信任的小 diff。

与 `how`、`why` 配套。`how` 告诉你代码做什么。`why` 告诉你为何如此设计。爆炸半径告诉你在别处会破坏什么。

列出 caller 不是本 job。Agent 一秒就能 grep 那些。本 job 是 grep 看不出来的破坏。

## 别只信自己写下来的分析

听起来对的爆炸半径分析毫无价值。无论真假都同样 convincing。所以不要交回分析文。找出整件事依赖的一两条事实，通过运行代码证明它们。

### 你有几分把握

对变更安全所依赖的每条事实，尽可能廉价地推到下列层级，并说明停在哪一层。

1. 你说如此。单独无价值。
2. 你指向那一行。真实 `file:line`，或库自身源码。
3. 你说明坏情况不会发生。逐步走 failure 路径，走不到。
4. 你运行了。脚本或测试调用真实代码，错了就 loud fail。
5. 你在运行中的应用里复现了。

第 4 步通常是一个小脚本，import 与应用相同的库，调用你担心的确切函数。

## 步骤

1. 读变更。Diff、新增/修改/删除的 symbol，以及行为差异，含 diff 未写明的部分。用 `why` 步骤 2 拉 PR 和 commit。
2. 找出安全所依赖的那一条事实。多数看起来 risky 的变更因单条事实而安全，如「此调用只丢弃已死 cache entry，不做别的」。找出该事实。若成立，多数 risky case 一次清掉。时间花在这里，不要花在长长的 maybe 列表。
3. 看 grep 停在哪里。读所调库的源码，查锁住的依赖版本及本地 patch。弄清何时运行：microtask、unmount/teardown、Solid vs React。跟 symbol search 漏掉的：API 返回的 JSON、DB 列、wire format、另一语言读同一字节、feature flag、三跳 downstream 的代码。
4. 诚实评估每个风险。给真实发生概率和真实代价。保留已确认的风险。单独列出已查且已排除的。同 `why` 规则。引用真实 `file:line`，搜不到也是答案，绝不编造 caller 或 API。
5. 证明那一条事实。写脚本或测试跑真实代码，运行，粘贴结果。
6. 对大或影响面宽的变更，以 `arena` 运行。问多个模型同一问题并合并答案。不同模型抓到不同 real bug。

## 交回什么

- **做了什么。** 改了什么，含不显然的部分。
- **安全所依赖的那一条事实。** 陈述，说明推到哪一步，展示证明。若无法证明，标 unproven。
- **风险。** 每条说明如何破坏、`file:line`、可能性与严重性、如何检查。重要者粘贴证明。
- **已排除。** 查了什么、为何没问题。
- **合并前。** 抓 real bug 的最廉价 test 或 repro，含你写的脚本。

用 `unslop` 写，引用真实代码，公开前 strip 私密内容。

**回复：** 上述内容，那条安全事实要么已证明要么标 unproven。
