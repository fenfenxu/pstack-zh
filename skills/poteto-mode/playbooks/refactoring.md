### Refactoring

**你负责契约。结构变，行为不变。** 与 Feature（新增行为）和 Bug fix（纠正行为）不同。

若清理暴露缺失功能或真实 bug，拆出并在已经固定下来的行为约定下先 ship 结构变更。允许 redesign，但须命名并路由到 Feature。大型或跨切面结构工作属于 **figure-it-out** skill。本 playbook 用于聚焦到中等的变更。

1. 先把行为约定固定下来。对受影响子系统运行 **how** skill 以了解契约，然后在任何结构移动前写 characterization test、snapshot 或等价 harness 捕获当前行为。若该区域无覆盖，动结构前先写能固定当前行为的测试。类型检查和 lint 不算把行为固定下来。
2. 按 **principle-model-the-domain** 指出代码缺失的结构。形状已清晰且局部时，简单直白的代码可保留。reshape 须删除分支或非法状态，不要增加间接层。
3. 命名目标形状。说明若今天新建，模块布局、类型与 call graph 应如何（**principle-foundational-thinking**、**principle-redesign-from-first-principles**）。若目标跨函数边界，移动前用 **architect** skill 并行探索形状设计。
4. 先减后增。引入新形状前删除 dead code、合并单 caller wrapper、去掉冗余 validator、移除 orphan reference（**principle-subtract-before-you-add**）。到达目标形状的最小变更即 ship（**principle-laziness-protocol**）。「可能有帮助」的 speculative cleanup 须 revert。
5. 以小步行为保持移动，每步都让这些固定行为的测试通过。API reshape 时，同一波次迁移每个 caller 并删除旧 API（**principle-migrate-callers-then-delete-legacy-apis**）。不要兼容 shim，不要新旧并行路径。对每个 rename 对照实际文件抽查。rename 会静默漏掉 string、正文与 back-reference 中的用法。将机械编辑委托给子代理，使用配置的 refactoring 模型（默认 `grok-4.7-xhigh-fast`），范围明确（file path、移动的名称、须保持的行为）。
6. 在真实产物上证明行为未变，而非「能编译」（**principle-prove-it-works**）。较大 reshape 时运行等价检查：diff 旧新输出的脚本、对新代码 replay 的记录基线，或通过相关 control skill 在匹配界面上做冒烟运行。
7. 确认变更值得保留。成功度量是降低 reader load（**principle-minimize-reader-load**）。若 diff 未在任何处降低 reader load，revert。
8. Rebase 成小而有序的 commit：减法 commit，然后 reshape，然后后续 cleanup。用 **sequence-verifiable-units** 原则 skill 塑形，使每个行为保持 slice 在下一步前保持绿。运行 **Opening a PR**。

**回复：** 变更的结构、这些测试所依据的约定、等价证明、reader load 变化、已 ship 与已 revert 的内容。无新行为。
