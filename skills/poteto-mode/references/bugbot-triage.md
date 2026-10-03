# Bugbot 评论分诊

Babysit playbook（`../playbooks/babysit.md`）处理 Bugbot 或其他自动评审评论时，用这份参考。目的不是默认无视 Bugbot，而是不再把每条评论都当成必须改代码。

## 怎么判断

动手之前，先给每个 Bugbot 讨论串归类：

- `fix`：评论指出了一个说得通的问题，涉及正确性、安全、隐私、数据丢失、鉴权、计费、迁移、幂等、竞态，或者已经发布出去的行为。在负责这处代码的最底层 PR 里修好，然后回复 commit SHA，并把讨论串标为已解决。
- `dismiss`：评论符合一个已经记录在案的低风险噪音模式，而且当前的代码和上下文能证明这个担心不需要改代码。用一句话说明理由，并把讨论串标为已解决。
- `ask`：评论是新情况、严重程度高、和安全、隐私或数据有关，或者意思含糊。问用户，不要猜。

拿不准就问。跳过一条噪音式的代码质量评论代价很小，跳过一个真实的数据或安全 bug 代价就大了。

## 记录新模式的格式

以后加新模式，按这个格式写：

```markdown
### <short pattern name>

- Confidence: candidate | recurring | strong
- Skip when: <conditions that must be true>
- Do not skip when: <risk boundaries>
- Example signal: <phrases or code context that identify the pattern>
- Source: <PR/comment URL or short historical note>
```

只有一两个例子时用 `candidate`。真实地驳回过好几次之后用 `recurring`。只有模式范围很窄、反复验证过、风险又低时，才用 `strong`。

## 反复出现、可以考虑跳过的模式

### 有意为之的 UI 或设计系统视觉改动

- Confidence: candidate
- Skip when: PR 描述、截图、设计评审或附近的代码已经说明这是有意的视觉改动，而 Bugbot 的评论只是在复述某个共享的视觉默认值变了。
- Do not skip when: 评论指向无障碍、焦点是否可见、键盘导航、颜色对比度，或者 PR 并非有意改动的组件 API 约定。
- Example signal: 关于焦点轮廓、按钮尺寸、间距或共享组件视觉默认值的评论，负责人回复「intentional」或「intended」。

### Bugbot 看不到 stack 上层或 stack 内部的用法

- Confidence: candidate
- Skip when: Bugbot 标出某个导出、组件、辅助函数或文件没被用到，而当前代码托管平台上的 PR 列表和 diff、stack 上层的 diff 或 PR 上下文显示，stack 里后面的某个 PR 用到了它。
- Do not skip when: 当前 PR 不属于任何 stack，这个符号是公开 API，或者所说的上层用法无法核实。
- Example signal: 「Exported component is never used」，人回复「used upstack」。

### 新旧并行实现期间的临时重复

- Confidence: candidate
- Skip when: PR 有意重复一小段代码，让新路径和一条正在删除、替换或验证的旧路径并行。
- Do not skip when: 重复的代码改变了安全、计费、数据访问或 API 行为，或者做成一个长期共享的抽象显然能降低风险。
- Example signal: 「Significant duplication」或「duplicated validation logic」，负责人解释旧路径会被删掉，或者这段重复逻辑是有意只放在本地。

### 现有框架或组件的 invariant 已经覆盖了这条警告

- Confidence: candidate
- Skip when: 评论担心的事，已经由当前 diff 或附近代码里看得到的共享组件、框架约定、类型 invariant 或唯一数据来源保证了。
- Do not skip when: 这个 invariant 只是假设、并没有强制，依赖时序，或者跨越了异步或状态边界、值有可能分叉。
- Example signal: 评论说内层 popover 缺少 max-height，而共享的 popover 已经强制限制在视口内；或者评论说可空值有问题，而本地检查的值和传下去的值来自同一个来源。

### 负责人声明的后续事项或推迟的清理

- Confidence: candidate
- Skip when: PR 负责人明确说这个问题是已知的后续事项，当前 PR 没有让行为变得更糟，而且评论不涉及高风险领域。
- Do not skip when: agent 在没有负责人表态的情况下行动，问题是中等或高严重程度的产品行为，或者推迟处理会把一个新的退化合进去。
- Example signal: 「I'll worry about that later」或「we'll delete this eventually」。

### 自行撤回或明确说是误报的规则类评论

- Confidence: recurring
- Skip when: 评论正文或 Bugbot 后来的回复明确说这条 finding 已撤回、已经合规或是误报，而且 agent 能在本地核实相关规则。
- Do not skip when: 唯一的证据是有人对一个高风险问题说了句「false positive」，却没有解释。
- Example signal: 一条文件命名规则的评论，正文里写着这个文件已经合规。

## 默认要问

下面这几类不要自动跳过，即使之前的 PR 驳回过类似的评论：

- 安全、隐私、鉴权、计费、数据保留、训练数据和权限边界方面的 finding。
- 严重程度高的 finding。
- 迁移、schema、幂等、并发和跨系统行为方面的 finding。
- 建议的修法很小、明显能降低风险、又不改变产品意图的评论。

历史数据显示，人有时会驳回安全或数据流方面的评论。把这些当作负责人各自的判断，不要当作全团队都适用的跳过规则。

## 最近几次 babysit 得到的候选经验

在 babysit 过程中或之后，发现看起来对团队有用、但还不够成熟的经验，就加在这里。等好几个 PR 都证实了某个模式，优先把它升到上面那一节。

### 手工重写浏览器原生行为

- Confidence: candidate
- Skip when: 几乎永远不跳过。当 diff 用手写的等价实现替换浏览器原生行为时（原生 sticky → 用 JS 定位的克隆元素，原生滚动目标 → 转发 wheel 或 touch 事件，按绘制顺序的遮挡 → mask 或 clip-path），Bugbot 针对这类代码提出的逻辑 bug finding 一直都是成立的。
- Do not skip when: finding 涉及这类代码里事件转发的缺口（wheel 的 deltaMode、触摸平移、到边缘时的滚动链、点击容差）、mask 或 clip 与命中测试不一致，或者 observer 和 React 状态之间的时序竞态。默认修。
- Example signal: 「masks do not affect hit-testing」「overlay blocks wheel scroll」「ignores deltaMode」「runs in the IntersectionObserver callback before React applies state」。
- Source: 一个处理 sticky 遮挡的 PR：Bugbot 跑了六轮，大约十八条 finding，每一条都修了，没有一条驳回。

### 说约定测试和文档对不上，很容易核实，先跑测试

- Confidence: candidate
- Skip when: 核实这一步本身永远不跳过，它只要一条命令。当 PR 带了一个把协议或文档原文写死的约定测试（对 SKILL.md 做正则匹配、对文档措辞做快照），而 Bugbot 说「测试和文档对不上了」（或者反过来）时，先在 PR 最新的提交上跑这个测试，再归类。测试红了，就用事实证实了这条评论；测试绿了，就是驳回回复里一个具体的反证。
- Do not skip when: 不适用，这是一条核实的捷径，不是驳回模式。注意，「跑过好几轮就倾向于驳回」的经验法则在这里会失灵：把文档原文写进测试的测试之所以会对不上，恰恰是因为前面几轮修复改了那段文字。
- Example signal: 「Contract test omits the pre-fix wait」，而这个 PR 前面的修复提交改写了被测试写死的那段文字；在最新提交上跑测试，正好在被引用的那条断言上失败。
- Source: 一个把文档原文写进测试的 PR，Bugbot 跑了八轮；尽管之前每一轮都修了、解决了，第 7 轮的这条评论仍然是真的。

### 过时的安全评审 finding，同一个 PR 后面已经修了

- Confidence: candidate
- Skip when: agentic 安全评审（或类似工具）说缺少一个鉴权或校验调用，而当前 PR 最新的提交里明明已经有这道检查（还带测试），通常是在评审跑完之后、一个加固提交里加上的。
- Do not skip when: 被引用的辅助函数对讨论中的那类调用者什么都不做，检查在它要防护的副作用之后才执行，或者评论提到的那类调用者没有测试覆盖。
- Example signal: 一条 HIGH 级别的「missing authorization check」finding，而最新提交里，那道检查已经在副作用之前被调用了。
- Source: 一个 webhook 端点的 PR，它的加固提交晚于评审运行的时间。

### 刻意收窄的错误条件不要放宽，否则会掩盖真正的错误

- Confidence: candidate
- Skip when: finding 要求把一个很窄的错误条件（某个特定的 `errno`、错误码或状态码类别）放宽成全盘捕获，而这种窄恰恰体现了一个真实的区分。典型的形态是只在 `ENOENT` 时才走依赖的备用方案：「程序没装」和「命令跑了但失败了」是两种不同的情况。只要退出码非零就重试，会把一个正常的失败（找不到、鉴权过期、网络问题）拿去用备用方案再跑一遍，然后报告备用方案的错误，把真正的错误藏起来。
- Do not skip when: 窄条件漏掉了同一类里的情况（另一个表示「程序用不了」的 errno，例如 `EACCES`，或者另一种传输层的失败），没处理的路径会丢数据或留下半截状态，或者重试是幂等的、而且原来的错误照样会报出来。
- Example signal: 「only retries when X fails with ENOENT … never tries the fallback even when a working Y exists」，指向的代码里，备用方案是为缺少依赖准备的，而不是为操作失败准备的。
- Source: 一个给 CLI 改名的 PR，它的备用方案是为程序不存在准备的，而不是为命令失败准备的。
