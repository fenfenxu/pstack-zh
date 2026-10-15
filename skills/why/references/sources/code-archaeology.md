# 代码考古（git + 仓库内）

## 这个来源里有什么

- 提交历史（信息、日期、作者、diff）
- PR 描述、评审评论、讨论串（通过 `gh`）
- 行内代码注释、TODO、FIXME、弃用说明
- ADR（架构决策记录），若仓库有维护
- 测试。名称和断言常常记下促使这次改动的边界情况
- 同提交中修改的相关文件（共变信号）
- 仓库里的 CHANGELOG 条目和发布说明
- 提交说明和 PR 正文里的工单 ID

最可信、与代码直接绑定，也最完整。经仓库的一切应在此。

## 怎么搜

扩展种子提交列表：

```bash
# 含重命名的历史
git log --follow --oneline -- <file>

# Pickaxe：添加或删除某段精确文本的提交
git log -S '<exact_string_from_code>' -- <file>

# 或按模式：
git log -G '<regex>' -- <file>

# 每行作者与时间
git blame -L <start>,<end> <file>

# 一次提交的完整 diff
git show <hash>

# 两时间点之间影响该文件的提交
git log <old>..<new> -p -- <file>
```

查找仓库外文档：

```bash
# ADR 常在 docs/adr/ 等
rg -l -i 'architecture.decision' --glob '*.md'

# 目标附近的 TODO 与 FIXME
rg -n -C2 '(TODO|FIXME|HACK|XXX|NOTE)' <target_file>

# 相关测试
rg -l '<symbol>' --glob '*test*'
```
