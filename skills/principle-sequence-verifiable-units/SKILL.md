---
name: principle-sequence-verifiable-units
description: "适用于多步工作（sweep、migration、相似编辑批次）以及 commit 和 PR 的堆叠方式。把工作拆成以可验证 state 结束的小单元，在下一步前检查当前单元，并按顺序交付使序列对审查者自证。"
disable-model-invocation: true
---

# 将工作编排为可验证单元

把工作排序为一系列小单元，每个以可检查 state 结束，当前单元 green 之前不要前进。

**原因：** 在造成 break 的单元处捕获 break，定位成本低。批次后再捕获则已被埋没，且你已在 broken base 上继续构建。把同样单元编排成审查者可 replay 的交付，把「相信我」变成「看它变红，再变绿」。

**执行。** 在 sweep、migration 或任何相似编辑 run 中，开始下一个前先验证每个变更。每个单元是 before/after bracket：known-good state、一次变更、跑 check、再 proceed。先 rebase 到 clean trunk，使每次 check 针对真实 baseline。当 lever 做编辑时，per-unit check 几乎免费。仍要跑。

**交付。** 按能证明工作的顺序 stack commit 和 PR。canonical 形状是先 failing test，再 fix 在上。其他 story order 可以是 reshape 前的 subtraction、treatment 前的 baseline capture、feature 前的 scaffold。每个 commit 独立落地，序列读起来像论证。

与 **prove-it-works** principle skill（让每次 check 真实）和 **build-the-lever** principle skill（让 per-unit check 便宜）形成 sequencing 互补。
