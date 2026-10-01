---
name: automate-me
description: "用于「automate me」「create/update/refresh my -mode skill」「turn/capture my preferences or working style into a skill」，或希望 agent 按用户工作方式行事时。通过 create-skill + unslop 起草或修订个人 -mode skill，可选从近期 transcript 拉取新证据。"
disable-model-invocation: true
---

# Automate me

将用户工作惯例转化为 agent 会遵循的 skill 的引导流程。输出是一个为其定制的 `-mode` skill（如 `jay-mode`、`priya-mode`）。

本 skill 编排另外三个：内联挖掘（见步骤 1）、Cursor 内置 `create-skill`（撰写）、以及 **unslop** skill（行文纪律）。它负责编排顺序，不替代它们。

## 流程

### 0. 检查是否已有 skill

递归查找 `.cursor/skills/**/*-mode/SKILL.md` 和 `~/.cursor/skills/*-mode/SKILL.md`，匹配用户 handle。Mode skill 可位于个人分类目录（`.cursor/skills/<handle>/`），不限于顶层。若已存在，用 AskQuestion 确认意图（除非用户已说「update my skill」等）：

- 更新现有 skill（重复运行的默认）
- 从头开始（少见，先问原因）

更新模式会改变后续流程：
- 步骤 1 仅挖掘 skill 上次编辑后的历史（`git log -1 --format=%cI <path>`）。
- 步骤 2 问什么变了或缺什么，而非从零问要 capture 哪些惯例。
- 步骤 4 原地编辑现有文件。保留用户未否定的章节。修订有新证据的章节。仅对真正新规则新增章节。

### 1. 挖掘历史

在扇出前定位当前 workspace 的 transcript。系统 prompt 会给出 workspace 的 `agent-transcripts/` 目录。只用该路径。不要 glob `~/.cursor/projects/*/`。那会跨 workspace 边界并读取无关项目的私密聊天。

在该范围内调查近期 agent 对话中的反复模式。对历史切片并行跑多个子 agent（如最近 2–4 周，拆成 3 片使每片有足够材料）。每个切片挖掘子 agent 从父 agent 提供的 workspace 范围路径读 transcript，寻找下方信号，返回带证据指针的简短结构化模式列表。默认值得搜寻的信号：

- 回复偏好（长度、语气、格式、「说简单点」类纠正）
- 委派习惯（子 agent、模型、专用 workflow、并行）
- 验证姿态（「done」的含义、单测 vs 现场复现、reviewer）
- 代码与行文纪律（风格、引用的原则、lint/format 工具）
- 流程惯例（worktree、commit、PR、review/merge 工具）
- 元偏好（任务中修 skill、提议新 skill）

在提升信号前跨切片交叉核对。2+ 切片出现的模式为高置信。孤立信号弱，通常丢弃。

### 2. 直接问用户

挖掘会漏掉尚未出现的意图。用 AskQuestion 工具（结构化多选），不要让用户从零打字。

形式：一两道题，每题 4–6 个选项，分类题设 `allow_multiple: true`。先宽（「哪些领域最重要？」），再对选中领域用具体选项跟进。结构化轮次后，一道自由形式聊天题捕获选项遗漏的内容。

不要一次问 20 个问题。

### 3. 聚类发现

将合并信号分组为章节。常见章节（仅用适用的）：

- **回复风格**：长度、语气、格式。
- **自主度**：不经询问做多少、MCP 工具使用。
- **先理解**：定范围或调查变更时该用哪些 skill。
- **子 agent**：默认、并行、模型与任务、专用 workflow。
- **行文/代码纪律**：原则、lint 工具、风格指南。
- **审查与验证**：复现姿态、verification skill、现场测试工具。
- **流程**：git worktree、commit、PR、review/merge 工具。
- **Skill**：skill 撰写习惯、先修 skill、提议新 skill。

**poteto-mode** skill 展示形态。读它以了解粒度。不要复制其内容。用户规则与 poteto-mode 不同。

### 4. 起草 skill

用 Cursor 内置 `create-skill` skill 撰写。放置：

- 路径：保留现有 mode skill 的分类。新 mode 时，若 repo 已有该 handle 的个人分类，用 `.cursor/skills/<handle>/<handle>-mode/SKILL.md`。否则默认项目内 `.cursor/skills/<handle>-mode/SKILL.md`（或用户偏好个人 skill 时用 `~/.cursor/skills/<handle>-mode/`）。
- Handle：用户名字或自选标识。
- Frontmatter `description`：以其名字 + `/<handle>-mode` +「按 ta 的风格工作」触发，不要用「write code」「review PR」等泛关键词。
- Frontmatter 格式：遵循 `create-skill` 的 YAML 规则。`description` 保持为单个 YAML 标量。需要时用引号或 `description: >-` 加缩进续行。
- Frontmatter `disable-model-invocation: true` 为默认。仅当用户明确希望 mode 每轮生效时才 opt out。

### 5. 迭代行文

对每一行应用 **unslop** skill 和 `create-skill` 的写作指南。

向用户展示草稿并收反馈。预期多轮迭代。狠心删减。Mode skill 不是手册。

### 6. 落地

在 main 的 worktree 中工作。Commit 并开 PR。不要直接 push 到 main。

## 护栏

- **不要过度拟合单次对话。** 说过一次又在别处矛盾的是噪声。编码前需多次出现。
- **不要耍聪明。** 复述其他 skill 内容、发明隐喻、为 agent 读者写「诗意」散文，成本无收益。保持可执行、可操作。
- **引用，不要内联。** 用户依赖的其他 skill 以路径引用出现，不要粘贴摘录。同理其在他处维护的原则文档。
- **章节保持精简。** 仅当用户在该处有具体、非默认规则时才加章节。「沟通要清楚」不是章节。「短段落。比较选项时用表格。仅当条目真正并列时才用 bullet。」才是。
- **命名惯例通用化。** 祈使句中用「用户」或「人类」，不用作者名字。
- **不要强行对称。** 若用户没有值得写下的流程规则，整节「流程」跳过。

## 评估

`-mode` skill 是主观输出。`create-skill` 式测试/迭代基准循环在这里无用。与用户感受核对：读起来像 ta 吗？漏了什么？然后 ship。

仅当 skill 的 trigger 准确性在实践中成问题时才跑 description-optimization 循环。

## 什么时候别用

- 用户要任务专用 skill（非工作惯例）：单独 `create-skill`，无需挖掘。
- 用户要固化一条窄 workflow（如「我怎么写 commit message」）。那是普通 skill，不是 mode skill。
