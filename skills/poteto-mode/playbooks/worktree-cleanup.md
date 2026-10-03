### 清理 worktree 和模拟器

**磁盘和安全关卡由你负责。** 清掉已经合并或已经放弃的 git worktree，以及过时的 iOS 模拟器，腾出空间。删除不可逆，所以每一步都要防着删掉正在用的东西，或者还有没提交改动的东西。

1. 记下现状，做一次盘点。记下 `df -h /` 的结果，然后运行 `scripts/worktree-audit.sh`（principle-build-the-lever）。它从 `git worktree list` 读取路径，从不手敲，因为手敲的 `myrepo-worktrees/x` 会漏掉放在 `.cursor/worktrees/myrepo/x` 的那个（principle-encode-lessons-in-structure）。它按大小、存在时长、合并状态、未提交的改动、PR 状态，以及最近碰过它的聊天，给每个 worktree 分类，并建议归到哪一类。扫对话记录很慢，放到后台跑。
2. 建议的分类只是建议，不是许可。置顶的和正在进行的聊天才是真正的依据（principle-prove-it-works）。从用户或侧边栏拿到这份名单，逐个核对每个候选。这个工具曾经把用户置顶聊天里的 worktree 标成 `safe`，所以以置顶名单为准。
3. 删除之前确认有没有在用。每一行 `verify-recent-chat`，以及任何你拿不准的，都开子代理去读对应的对话记录，报告这个聊天是置顶的还是还在进行，以及它碰了哪些 worktree（principle-guard-the-context-window，对话记录量很大）。置顶的聊天会通过后台子代理，在相邻的 worktree 里建出 arena 和复现用的目录，这些目录的名字即使从没出现在侧边栏，也是在用的。
4. 碰到不可逆的损失就暂停。`wip:N` 表示有 N 处被跟踪、但还没提交的改动。先把 diff 拿出来，等对方决定，因为删掉一个干净的 worktree 还能从它的分支找回来，没提交的改动删了就没了。`scratch:N` 是没被跟踪的临时文件，可以放心删，但要列出文件名。按 Autonomy，干净、已合并、没在用的，直接继续。`wip` 的和在用的，暂停。
5. 清掉确认过的那一批。每个路径执行 `git worktree remove --force <path>`。如果目录因为被忽略的构建产物还留着，就 `rm -rf` 它，然后 `git worktree prune`。分支的 ref 还在，所以不会丢任何 commit。用 `df -h /` 确认，再重新列一遍。
6. 模拟器和其他能腾出空间的地方。模拟器通常是第二大头。`xcrun simctl --set testing delete all`（XCTestDevices 的克隆）、`xcrun simctl delete unavailable`，以及先 `xcrun simctl runtime list`、再对旧的 runtime 执行 `runtime delete <id>`。需要的话还有：Xcode 的 `DerivedData` 和 `iOS DeviceSupport`、`~/Library/Application Support/Cursor`（`state.vscdb.backup`，以及 `snapshots/roots/<root>`，其中以你当作 workspace 打开过的文件夹命名的 `<root>` 会膨胀得很大）、各种包缓存（pnpm、uv、brew、yarn）。只清用户没说要保留的缓存。

这是唯一一个会删除用户状态、却没有代码评审来兜住失误的 playbook，所以上面这些关卡就是评审。

**回复：** 清理前后的 `df -h /` 和腾出的空间、清掉了哪些 worktree，以及每个留下来的用一行说明原因（被哪个聊天在用，或者有没提交的改动）。
