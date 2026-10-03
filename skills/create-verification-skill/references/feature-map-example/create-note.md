# 创建笔记

用户可以用创建笔记功能在浏览器或 CLI 里保存一条带标题的笔记，放弃还没写完的草稿，并在第二个面向用户的界面里确认笔记已经存下。

## Sub-features

- `create-open` 从浏览器的每个入口打开空白编辑器。
- `create-save` 保存标题和正文。
- `create-cancel` 丢弃浏览器里没写完的草稿。
- `create-cli` 在终端里创建同样结构的笔记。

## How to get to it (user POV)

- 在浏览器工具栏里点 `New note` 按钮。
- 焦点不在可编辑区域时，在浏览器里按 `n`。
- 在终端里运行 `notes create --title <title> --body <body>`。

## Driving it with control-notes

Preconditions:

- Notes 在 `http://127.0.0.1:4173` 运行正常。
- 没有标题为 `Release checklist` 的笔记。
- `control-notes doctor` 报告的 URL 和用完即弃的数据目录都符合预期。

- **打开编辑器。** 点 `New note`。运行 `control-notes browser click --role button --name "New note"`。出现名为 `Note editor` 的表单，焦点在 `Title` 输入框里。
- **填写内容。** 输入标题和正文。运行 `control-notes browser fill --role textbox --name "Title" --value "Release checklist"` 和 `control-notes browser fill --role textbox --name "Body" --value "Tag and publish"`。`Save note` 按钮变为可点。
- **保存笔记。** 点 `Save note`。运行 `control-notes browser click --role button --name "Save note"`。出现名为 `Note saved` 的状态提示，标题显示为 `Release checklist`。
- **确认已保存。** 回到笔记列表，再打开这条笔记。运行 `control-notes browser click --role link --name "All notes"` 和 `control-notes browser click --role link --name "Release checklist"`。编辑器里两个值都在。
- **放弃草稿。** 新建一条笔记，输入 `Discard me`，点 `Cancel`。运行 `control-notes browser click --role button --name "New note"`、`control-notes browser fill --role textbox --name "Title" --value "Discard me"` 和 `control-notes browser click --role button --name "Cancel"`。回到笔记列表，列表里没有 `Discard me` 链接。
- **CLI 入口。** 再建第二条笔记。运行 `control-notes cli -- notes create --title "CLI note" --body "Created from terminal" --format json`。退出码为 `0`，stdout 里有新笔记的 ID 和标题。
- **证明。** 从 `All notes` 重新打开两条已保存的笔记。运行 `control-notes browser snapshot --aria --path artifacts/create-note/list.aria.txt` 和 `control-notes browser screenshot --path artifacts/create-note/list.png`。证明材料里能看到 `Release checklist` 和 `CLI note`。

## Gotchas

- 输入框有焦点时按 `n`，会打出这个字母，而不是打开新编辑器。
- 保存时标题首尾的空白会被去掉。断言渲染出来的标题，不要断言草稿里输入的值。
- 光有保存成功的提示不足以证明。要从列表重新打开这条笔记。
- 清理测试数据时删掉 `Release checklist` 和 `CLI note`，但保留它们的证明材料。
