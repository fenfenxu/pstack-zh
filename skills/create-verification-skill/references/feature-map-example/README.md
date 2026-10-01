# Notes 验证 map

本目录是 Notes 面向用户行为验证的维护源。驱动应用前先读索引，再按对应功能文件作配方。

## 基线前置条件

- 在 `http://127.0.0.1:4173` 启动 Notes，使用可丢弃数据目录。
- 设置 `NOTES_DATA_DIR=/tmp/notes-verify-$RUN_ID`，使并发 run 不共享状态。
- 种子笔记标题 `Quarterly plan` 与 `Grocery list`。
- 将 `control-notes` 与 `notes` CLI 加入 `PATH`。
- 运行 `control-notes doctor`，要求 URL、数据目录与构建 revision 符合预期。
- 不要驱动非本验证 run 启动的实例。

## 驱动约定

- 除非某配方前置条件另有说明，每条配方从基线状态开始。
- 优先 ARIA role 与可访问名称，而非 CSS selector 或 DOM 位置。
- 每条命令按字面执行。引号内名称与 flag 保持不变。
- 浏览器操作经 `control-notes browser`。
- 终端操作经 `control-notes cli -- <command>`。
- 变更后恢复种子数据。cleanup 时不要删除证明产物。

## 证明与跳过报告

- 采集用户动作与结果状态，不只最终画面。
- UI 证明含 ARIA snapshot 与应用身份可见的截图。
- CLI 证明含命令、stdout、stderr 与退出码。
- 变更证明含存储值的只读二次查看。
- 每条 artifact 记录功能 ID 与所用入口。
- 不可达路径报告尝试的命令与未满足的前置条件。
- 不要把跳过的入口报告为经其他路径已验证。

## 功能条目要写什么

每个功能文件以 H1 标题开头，一段描述用户可见行为。随后严格按序四个 H2：

1. `Sub-features` 列短 ID，每行为一行为。
2. `How to get to it (user POV)` 列每个用户入口。
3. `Driving it with <harness>` 以 `Preconditions:` 开头，用带标签 bullet 将每个用户动作与确切命令及可观察结果配对。
4. `Gotchas` 列可能浪费或使验证失效的陷阱。

map 中不写实现细节。只写用户路径、稳定句柄、所需状态、命令与可观察证明。

## 功能

- [创建笔记](./create-note.md) 覆盖浏览器与 CLI 创建、取消、持久化与 cleanup。
- [搜索笔记](./search.md) 覆盖工具栏、键盘与 CLI 搜索的匹配、空结果与清除状态。
