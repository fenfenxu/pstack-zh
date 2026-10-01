### Worktree 与模拟器清理

**你负责磁盘与安全门。** 清理已合并或废弃的 git worktree 与陈旧 iOS 模拟器以回收空间。删除不可逆，因此每一步都防止删掉在用项或含未提交工作的项。

1. 快照与审计。记录 `df -h /`，然后运行 `scripts/worktree-audit.sh`（principle-build-the-lever）。它从 `git worktree list` 读取路径，不要手输，因为手输的 `myrepo-worktrees/x` 会漏掉位于 `.cursor/worktrees/myrepo/x` 的 worktree（principle-encode-lessons-in-structure）。它按大小、年龄、合并状态、未提交工作、PR 状态及最近触及该 worktree 的聊天分类，并建议 bucket。转录扫描较慢，可后台运行。
2. bucket 是建议，不是许可。已固定与活跃聊天才是真实依据（principle-prove-it-works）。从用户或侧边栏取得该集合并交叉核对每个候选。杠杆可能将用户已固定的 worktree 标为 `safe`，因此已固定集优先。
3. 删除前验证使用情况。对每个 `verify-recent-chat` 行或任何存疑项，派子代理读转录并报告聊天是否已固定或进行中，以及触及哪些 worktree（principle-guard-the-context-window，转录体量大）。已固定的聊天会通过后台子代理在兄弟 worktree 中生成 arena 与 repro 树，即使用中时名称未出现在侧边栏。
4. 不可逆损失处暂停。`wip:N` 表示 N 个已跟踪未提交编辑。先展示 diff 并取得决定，因清理干净 worktree 可从分支恢复，未提交工作则丢失。`scratch:N` 为未跟踪临时文件，可安全删除，但须列出文件名。按 Autonomy，clean、merged 且未在使用可继续。`wip` 与使用中项暂停。
5. 清理已确认集合。对每个 path：`git worktree remove --force <path>`。若目录因被忽略的构建产物仍存在，`rm -rf` 后 `git worktree prune`。分支 ref 保留，commit 不丢。用 `df -h /` 确认并重新列出。
6. 模拟器与其他回收项。模拟器通常是下一项最大收益。`xcrun simctl --set testing delete all`（XCTestDevices 克隆）、`xcrun simctl delete unavailable`，以及 `xcrun simctl runtime list` 后对旧 runtime 执行 `runtime delete <id>`。需要时还可清理：Xcode `DerivedData` 与 `iOS DeviceSupport`、`~/Library/Application Support/Cursor`（`state.vscdb.backup`，以及作为 workspace 打开的文件夹名 `<root>` 对应的 `snapshots/roots/<root>` 可能膨胀）、包缓存（pnpm、uv、brew、yarn）。仅清理用户未要求保留的缓存。

这是唯一在无 code review 把关的情况下删除用户状态的 playbook，因此上述门控即 review。

**回复：** 清理前后 `df -h /` 及回收空间、已清理的 worktree，以及每项保留的一行理由（被哪条聊天使用中，或含未提交工作）。
