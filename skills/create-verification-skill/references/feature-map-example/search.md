# 搜索笔记

搜索让用户按标题或正文查找笔记、查看匹配笔记，并区分无匹配与搜索不可用。

## Sub-features

- `search-open` 从各支持的浏览器入口打开搜索。
- `search-match` 返回标题与正文匹配，不改变笔记数据。
- `search-open-result` 在笔记编辑器中打开结果。
- `search-empty` 对无匹配查询显示完整空状态。
- `search-clear` 清除查询并恢复最近笔记视图。
- `search-cli` 从终端返回相同匹配笔记。

## How to get to it (user POV)

- 在浏览器工具栏选择 `Search` 按钮。
- 在浏览器中、焦点不在可编辑字段上时按 `/`。
- 在终端运行 `notes search <query>`。

## Driving it with control-notes

Preconditions:

- Notes 在 `http://127.0.0.1:4173` 健康。
- 可丢弃数据目录含正文为 `Draft budget` 的 `Quarterly plan`。
- `control-notes doctor` 报告预期 URL 与数据目录。

- **工具栏入口。** 选择 `Search` 按钮。运行 `control-notes browser click --role button --name "Search"`。名为 `Search notes` 的对话框出现，焦点在其 searchbox。
- **键盘入口。** 关闭对话框，聚焦页面，按 `/`。运行 `control-notes browser press --key "/"`。同一对话框出现，页面不插入斜杠。
- **标题匹配。** 输入 `quarterly`。运行 `control-notes browser fill --role searchbox --name "Search notes" --value "quarterly"`。`Search results` 列表含 `Quarterly plan`，不含 `Grocery list`。
- **正文匹配。** 将查询换为 `budget`。运行 `control-notes browser fill --role searchbox --name "Search notes" --value "budget"`。结果 `Quarterly plan` 仍可见，带正文匹配摘要。
- **打开结果。** 选择 `Quarterly plan`。运行 `control-notes browser click --role link --name "Quarterly plan"`。对话框关闭，编辑器标题为 `Quarterly plan`。
- **空状态。** 重新打开搜索并输入 `volcano`。运行 `control-notes browser fill --role searchbox --name "Search notes" --value "volcano"`。搜索完成后出现名为 `No matching notes` 的状态。
- **清除查询。** 选择 `Clear search`。运行 `control-notes browser click --role button --name "Clear search"`。searchbox 为空，`Recent notes` 区域取代结果列表。
- **CLI 匹配。** 从终端搜索。运行 `control-notes cli -- notes search "quarterly" --format json`。退出码 `0`，stdout 含一条 title 为 `Quarterly plan` 的对象。
- **CLI 未命中。** 搜索不存在值。运行 `control-notes cli -- notes search "volcano" --format json`。退出码 `0`，stdout 为 `[]`。
- **证明。** 采集有结果的状态。运行 `control-notes browser snapshot --aria --path artifacts/search/results.aria.txt` 与 `control-notes browser screenshot --path artifacts/search/results.png`。两个 artifact 均标识 Notes、查询与 `Quarterly plan`。

## Gotchas

- 编辑器或 searchbox 有焦点时按 `/` 会插入文字而非打开搜索。
- 结果在短 debounce 后更新。等结果列表或空状态，不要固定 sleep。
- 除非用户启用 `Include archived`，归档笔记被排除。
- CLI 默认人类可读输出。稳定断言用 `--format json`。
- 打开结果会改变浏览器状态。证明另一查询前须重新打开搜索。
