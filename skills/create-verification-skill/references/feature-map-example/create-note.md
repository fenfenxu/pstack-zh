# 创建笔记

创建笔记让用户从浏览器或 CLI 保存带标题的笔记、取消未完成草稿，并从第二个面向用户的视图确认已保存笔记。

## Sub-features

- `create-open` 从各浏览器入口打开空白编辑器。
- `create-save` 持久化标题与正文。
- `create-cancel` 丢弃未完成的浏览器草稿。
- `create-cli` 从终端创建相同形态的笔记。

## How to get to it (user POV)

- 在浏览器工具栏选择 `New note` 按钮。
- 在浏览器中、焦点不在可编辑字段上时按 `n`。
- 在终端运行 `notes create --title <title> --body <body>`。

## Driving it with control-notes

Preconditions:

- Notes 在 `http://127.0.0.1:4173` 健康。
- 无标题为 `Release checklist` 的笔记。
- `control-notes doctor` 报告预期 URL 与可丢弃数据目录。

- **打开编辑器。** 选择 `New note`。运行 `control-notes browser click --role button --name "New note"`。名为 `Note editor` 的表单出现，焦点在 `Title` textbox。
- **输入内容。** 输入标题与正文。运行 `control-notes browser fill --role textbox --name "Title" --value "Release checklist"` 与 `control-notes browser fill --role textbox --name "Body" --value "Tag and publish"`。`Save note` 按钮变为可用。
- **保存笔记。** 选择 `Save note`。运行 `control-notes browser click --role button --name "Save note"`。出现名为 `Note saved` 的状态，标题为 `Release checklist`。
- **确认持久化。** 返回笔记列表并重新打开笔记。运行 `control-notes browser click --role link --name "All notes"` 与 `control-notes browser click --role link --name "Release checklist"`。编辑器显示两个已保存值。
- **取消草稿。** 新建笔记，输入 `Discard me`，选择 `Cancel`。运行 `control-notes browser click --role button --name "New note"`、`control-notes browser fill --role textbox --name "Title" --value "Discard me"`、`control-notes browser click --role button --name "Cancel"`。返回笔记列表，无 `Discard me` 链接。
- **CLI 入口。** 创建第二条笔记。运行 `control-notes cli -- notes create --title "CLI note" --body "Created from terminal" --format json`。退出码 `0`，stdout 含新笔记 ID 与标题。
- **证明。** 从 `All notes` 重新打开两条已保存笔记。运行 `control-notes browser snapshot --aria --path artifacts/create-note/list.aria.txt` 与 `control-notes browser screenshot --path artifacts/create-note/list.png`。artifact 显示 `Release checklist` 与 `CLI note`。

## Gotchas

- textbox 有焦点时按 `n` 会输入字符而非打开新编辑器。
- 保存时标题会 trim。断言渲染后的标题，非草稿输入值。
- 仅保存状态不足以证明。须从列表重新打开笔记。
- fixture cleanup 时删除 `Release checklist` 与 `CLI note`，但保留其证明 artifact。
