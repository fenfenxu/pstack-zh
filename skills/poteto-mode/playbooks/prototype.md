### Prototype

**你拥有设计决策，不是代码。原型是可丢弃 instrument。真实 build 走 Feature。**

唯一 Laziness Protocol「最小变更」与验证 bar 反转的 playbook。速度优于 polish，代码质量不重要，不规划。严谨在于廉价选对设计。提议用户未要求的变体，扔掉一种再试另一种。

1. 界定原型要做的决策：哪种 layout、交互、density；或经验分叉上哪种行为、时序或方案。无决策则无原型，路由 Feature。
2. 设计空间开放时收集参考。搜 prior art，总结 moodboard（主题、palette、layout），让用户选方向再 build。方向已定时 skip。
3. 在隔离 scratch dir 搭 throwaway，与 production source 分离。视觉决策用 vanilla HTML/CSS/JS 或最轻能渲染想法的栈，CDN deps，带 hot reload 的 dev server。行为或时序决策用最小能 exercise 问题的脚本。无 production 框架、无测试、无抽象。
4. 比较备选时，在一个 switcher（按钮或按键）后 build，每变体 labeled。这是廉价版 **exhaust-the-design-space** principle skill。
5. 在匹配表面验证。视觉决策经 control skill 截图每变体并驱动交互。行为或时序决策通过 log 时序、打印输出或观察 render 来观察待决项。此处观察即测试，非 assertion。
6. 呈现备选、tradeoffs 与建议。输出是决策加 throwaway 人工制品，非可 ship 代码。将选定方向交给 **Feature**（或 `architect` 定形状）做真实 build。

**Reply：** 探索的变体、证据（视觉决策用截图，行为决策用观察输出或时序）、tradeoffs、建议、scratch 路径。明确说明原型可丢弃。
