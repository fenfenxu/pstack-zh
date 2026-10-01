# Bugbot 分类

Babysit playbook（`../playbooks/babysit.md`）处理 Bugbot 或 review 自动化评论时使用本参考。目标不是默认忽略 Bugbot，而是停止把每条评论都当作必须的代码变更。

## 决策准则

行动前对每条 Bugbot 线程分类：

- `fix`：评论指出 plausible 的正确性、安全、隐私、数据丢失、auth、billing、迁移、幂等、竞态或已 ship 行为问题。在最低 owning PR 中修复，回复 commit SHA 并 resolve 线程。
- `dismiss`：评论匹配已文档化的低风险噪音模式，且当前代码/上下文证明无需改代码。简短说明理由并 resolve 线程。
- `ask`：评论新颖、高严重度、安全/隐私/数据相关或 ambiguous。问用户，不要猜。

不确定时问。跳过噪音式代码质量评论成本低；跳过真实数据或安全 bug 成本高。

## 已学模式格式

未来模式按此形状添加：

```markdown
### <short pattern name>

- Confidence: candidate | recurring | strong
- Skip when: <conditions that must be true>
- Do not skip when: <risk boundaries>
- Example signal: <phrases or code context that identify the pattern>
- Source: <PR/comment URL or short historical note>
```

`candidate` 用于一两个例子。多次真实 dismiss 后用 `recurring`。仅当模式 narrow、反复验证且低风险时用 `strong`。

##  recurring skip 候选

### 有意 UI 或设计系统视觉变更

- Confidence: candidate
- Skip when: PR 描述、截图、设计 review 或邻近代码明确视觉变更，且 Bugbot 评论仅复述共享视觉默认已变。
- Do not skip when: 评论指向 accessibility、focus 可见性、键盘导航、颜色对比，或 PR 未有意变更的组件 API 契约。
- Example signal: 关于 focus outline、按钮尺寸、间距或共享组件视觉默认的评论，owner 回复「intentional」或「intended」。

### Bugbot 看不到的 upstack 或栈内用法

- Confidence: candidate
- Skip when: Bugbot 标记 export、组件、helper 或文件未使用，且 active forge 的 PR 列表与 diff、upper-stack diff 或 PR 上下文显示栈中后续 PR 在使用。
- Do not skip when: 当前 PR 不在栈中、符号是 public API，或所谓 upstack 用法无法验证。
- Example signal: 「Exported component is never used」，人类回复「used upstack」。

### 并行实现期间的临时重复

- Confidence: candidate
- Skip when: PR 有意重复少量代码，使新路径与正被删除、替换或验证的旧路径并行。
- Do not skip when: 重复代码改变安全、billing、数据访问、API 行为，或长期共享抽象明显能降低风险。
- Example signal: 「Significant duplication」或「duplicated validation logic」，owner 解释旧路径将删除或重复逻辑 intentionally local。

### 现有框架或组件不变量已覆盖警告

- Confidence: candidate
- Skip when: 关切已由共享组件、框架契约、类型不变量或当前 diff/邻近代码可见的单一真相源保证。
- Do not skip when: 不变量仅被假设未强制、依赖时序，或跨 async/state 边界值可能 diverge。
- Example signal: 内层 popover 缺 max-height，而共享 popover 已强制 viewport 边界；或可空值处 local checked 与 passed 值同源。

### Owner 声明的 follow-up 或 deferred cleanup

- Confidence: candidate
- Skip when: PR owner 明确说问题是已知 follow-up，当前 PR 未使行为更差，且评论非高风险区。
- Do not skip when: agent 在无 owner 输入下行动、问题为中/高严重度产品行为，或 defer 会 merge 新 regression。
- Example signal: 「I'll worry about that later」或「we'll delete this eventually」。

### 自行撤回或明确 false-positive 规则评论

- Confidence: recurring
- Skip when: 评论正文或后续 Bugbot 回复明确说 finding 已撤回、合规或 false positive，且 agent 可在本地验证相关规则。
- Do not skip when: 唯一证据是人类对高风险 issue 说「false positive」而无解释。
- Example signal: 文件命名规则评论正文称文件已合规。

## 默认 ask

以下类别不要 auto-skip，即使先前 PR dismiss 过类似项：

- 安全、隐私、auth、billing、数据保留、训练数据与权限边界 finding。
- 高严重度 finding。
- 迁移、schema、幂等、并发与跨系统行为 finding。
- 建议修复小且明显降风险且不改变产品意图的评论。

历史数据显示人类有时会 dismiss 安全/数据流评论。将其视为 owner 判断，而非团队级 skip 规则。

## 近期 babysit 的候选 learnings

babysit 期间或之后，若看起来对团队有用但尚未成熟，在此追加候选 learning。多个 PR 确认模式后，优先提升到上文 section。

### 手动重实现原生浏览器行为

- Confidence: candidate
- Skip when:  practically never。当 diff 用 manual 等价替换原生行为（native sticky → JS 定位 clone、native scroll targeting → 转发 wheel/touch、绘制顺序 occlusion → mask/clip-path），Bugbot 对该代码的逻辑 bug finding 一贯 legitimate。
- Do not skip when: finding 涉及 event-forwarding 缺口（wheel deltaMode、touch pan、边缘 scroll-chaining、tap slop）、mask/clip hit-testing diverge，或此类代码中 observer 与 React state 时序 race。默认 fix。
- Example signal: 「masks do not affect hit-testing」「overlay blocks wheel scroll」「ignores deltaMode」「runs in the IntersectionObserver callback before React applies state」。
- Source: 一个 sticky-occlusion PR：六次 Bugbot pass，约十八个 finding，全部 fix 而非 dismiss。

### 契约测试的 drift 说法很好查，先跑测试

- Confidence: candidate
- Skip when: 永不 skip 验证本身；成本一条命令。当 PR 里的契约测试把协议或文档原文写死（对 SKILL.md 的 regex、文档措辞 snapshot），且 Bugbot 称「测试不再匹配文档」（或反之），在 PR tip 上先跑该测试再分类。红 run  empirically 确认主张；绿 run 是 dismiss 回复的具体反证。
- Do not skip when: n/a — 这是验证快捷方式，非 dismiss 模式。注意 repeat-pass lean-dismiss 启发式在此会 misfire：把文档原文写进测试之后，测试发生漂移，正是因为 earlier fix 轮编辑了 prose。
- Example signal: 「Contract test omits the pre-fix wait」，PR 较早 fix commit 改写了被测试写死的段落；tip 上测试跑失败于 cited assertion。
- Source: 一个把文档原文写进测试的 PR 八次 Bugbot pass；第 7 次 pass 主张为真，尽管 earlier pass 均 fix-and-resolve。

### 同一 PR 后续 commit 已修复的 stale 安全 review finding

- Confidence: candidate
- Skip when: agentic 安全 review（或类似）称缺 authz/validation 调用，而当前 PR tip  clearly 含该 gate（含测试），通常于 review 运行后的 later hardening commit 添加。
- Do not skip when: cited helper 对讨论中的 principal 是 no-op、检查在 guarded 副作用之后运行，或缺 claimed principal 的 coverage。
- Example signal: HIGH「missing authorization check」，而 tip 上副作用前已调用 exact guard。
- Source: 一个 webhook-endpoint PR，hardening commit 晚于 review run。

### 放宽 deliberately narrow 错误条件会掩盖真实错误

- Confidence: candidate
- Skip when: finding 要求把 narrow 错误条件（特定 `errno`、error code 或 status class） broaden 为 catch-all，而该 narrowness 编码真实区分。典型形状是依赖 fallback  gated on `ENOENT`：「binary 未安装」与「命令已跑但失败」是不同情况。对任何 non-zero exit retry 会对 legitimate failure（not found、过期 auth、网络）重跑 fallback 并报告 fallback 错误，隐藏真实错误。
- Do not skip when: narrow 条件 miss 同类别 case（另一「binary 不可用」errno 如 `EACCES`、另一 transport-level failure）、未处理路径丢数据或留 partial state，或 retry 幂等且仍 surface 原始错误。
- Example signal: 「only retries when X fails with ENOENT … never tries the fallback even when a working Y exists」，指向 fallback 针对 missing dependency 而非 failed operation 的代码。
- Source: 一个 CLI-rename PR，fallback 针对 missing binary 而非 failed command。
