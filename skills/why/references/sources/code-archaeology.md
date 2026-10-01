# 代码考古（git + 仓库内）

## 这个来源里有什么

- 提交历史（信息、日期、作者、diff）
- PR 描述、评审评论、讨论串（通过 `gh`）
- 行内代码注释、TODO、FIXME、弃用说明
- ADR（架构决策记录），若仓库有维护
- 测试。名称与断言常编码驱动变更的边缘情况
- 同提交中修改的相关文件（共变信号）
- 仓库内的 CHANGELOG、发布说明
- 提交信息与 PR 正文中提及的工单/ticket ID

最可信、与代码直接绑定，也最完整。经仓库的一切应在此。

## 怎么搜

扩展种子提交列表：

```bash
# 文件完整历史（含重命名跟踪）
git log --follow --oneline -- <file>

# Pickaxe：添加或删除某段精确文本的提交
git log -S '<exact_string_from_code>' -- <file>

# 或按模式：
git log -G '<regex>' -- <file>

# 每行作者与时间
git blame -L <start>,<end> <file>

# 某提交的完整 diff
git show <hash>

# 两时间点之间影响该文件的提交
git log <old>..<new> -p -- <file>
```

对每个实质性提交，拉取 PR 上下文：

```bash
# 从合并提交或分支找 PR 编号
git log -1 --format=%B <hash>

# 完整 PR 上下文：正文、评审评论、关联 issue
gh pr view <number> --json title,body,author,createdAt,mergedAt,labels,closingIssuesReferences,comments,reviews,files

# --json 的 reviews 与 comments 字段是主要信号所在
```

查找仓库外文档：

```bash
# ADR 常在 docs/adr/ 等
rg -l -i 'architecture.decision' --glob '*.md'

# 目标附近的 TODO 与 FIXME
rg -n -C2 '(TODO|FIXME|HACK|XXX|NOTE)' <target_file>

# 相关测试。名称常编码「为什么」
rg -l '<symbol>' --glob '*test*'
```

## 这里怎样算好证据

- PR 描述解释要解决的问题，而非仅描述变更（「修复导致 X 的分页 bug」）
- 长篇评审串，其中辩论了替代方案
- 目标行附近解释非显然约束的行内注释
- 名为 `test_handles_edge_case_when_X` 的测试，揭示驱动代码的边缘情况
- 引用工单或事故 ID 的提交信息
- 概括用户可见理由的 CHANGELOG 条目

## 常见陷阱

- **Squash 合并平坦化。** 若仓库 squash PR，分支内单独提交会丢失。回退到 PR 正文与评论。
- **误导性提交信息。** 「小重构」有时掩盖有意行为变更。看 diff，不要只看信息。
- **照抄模式。** 作者可能复制模式而不懂为何。检查模式是否更早出现在代码库，调查*那个*提交。
- **Bot 提交与自动合并。** Dependabot、Renovate、自动 backport 通常不带动机。找意图时跳过。
- **把代码当意图证据。** 代码本身不能证明为何存在。证据来自提交信息、PR、评论、测试、文档。不要引用「函数名叫 X」作为意图证据。

## 要返回什么

每条与问题相关的提交/PR/评论，含：
- 确切文本（引用）
- hash / PR 编号 / file:line
- 作者与日期
- 是直接（直接回答问题）还是旁证
