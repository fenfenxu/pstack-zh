---
name: automate-me
description: "用于「automate me」「create/update/refresh my -mode skill」「turn/capture my preferences or working style into a skill」，或者希望 agent 照用户的方式做事。借助 create-skill 和 unslop 起草或修订个人的 -mode skill，可以选择从近期对话记录里补充新证据。"
disable-model-invocation: true
---

# Automate me

一套引导流程，把用户的工作习惯变成 agent 会遵守的 skill。产出是一个为用户量身定做的 `-mode` skill（例如 `jay-mode`、`priya-mode`）。

这个 skill 调度另外三样东西：一轮内嵌的挖掘（见第 1 步）、Cursor 内置的 `create-skill`（负责写 skill），以及 **unslop** skill（负责文字纪律）。它只安排先后顺序，不取代它们。

## 流程

### 0. 先看有没有现成的 skill

递归查找跟用户名号匹配的 `.cursor/skills/**/*-mode/SKILL.md` 和 `~/.cursor/skills/*-mode/SKILL.md`。mode skill 可能放在个人分类目录里（`.cursor/skills/<handle>/`），不一定在顶层。如果已经有了，用 `AskQuestion` 确认用户想做什么（用户已经说了「update my skill」之类的话就不用问）：

- 更新现有的 skill（重复运行时的默认）
- 从头再来（少见，动手前先问为什么）

选了更新，后面的流程会变：
- 第 1 步只挖这个 skill 上次修改之后的历史（`git log -1 --format=%cI <path>`）。
- 第 2 步问的是哪些变了、还缺什么，而不是从零问要记下什么。
- 第 4 步直接改现有文件。用户没有否定的章节保留。有新证据的章节修改。只有真正新的规则才加新章节。

### 1. 挖用户的历史

扇出之前，先找到当前 workspace 的对话记录。系统提示词里写了这个 workspace 的 `agent-transcripts/` 目录。只用这个路径。不要对 `~/.cursor/projects/*/` 做 glob。那样会越过 workspace 的边界，读到无关项目里的私人对话。

在这个范围内，梳理近期 agent 对话里反复出现的规律。把历史切成几段，开几个子代理并行挖（例如最近 2 到 4 周，切成 3 段，让每段都有足够材料）。每个挖掘子代理从父代理给的、限定在本 workspace 的路径读对话记录，找下面这些信号，返回一份简短的结构化清单，列出看到的规律和证据出处。默认值得找的信号：

- 回复偏好（长度、语气、格式，「说简单点」这类纠正）
- 委派习惯（子代理、模型、专门的工作流程、并行）
- 验证的态度（「做完」指什么，单元测试还是现场复现，评审的人）
- 代码和文字纪律（风格、常引用的原则、lint 和格式化工具）
- 流程习惯（worktree、commit、PR、评审和合并用的工具）
- 关于做事方式本身的偏好（任务中途修 skill、提议新 skill）

一个信号要在几段之间互相印证，才能升格。在 2 段或更多段里出现的规律，可信度高。只出现一次的信号很弱，通常丢掉。

### 2. 直接问用户

挖掘抓不到还没表现出来的意图。用 `AskQuestion` 工具（结构化的多选题），不要让用户从零开始打字。

形式：一两道题，每题 4 到 6 个选项，分类题用 `allow_multiple: true`。先问大的（「哪些方面最重要？」），再针对选中的方面给具体选项追问。结构化的几轮问完后，在聊天里再问一道开放题，接住选项没覆盖到的东西。

不要一口气抛出 20 道题。

### 3. 把发现归类

把合在一起的信号分成章节。常见的有这些（只用适用的）：

- **回复风格**：长度、语气、格式。
- **自主程度**：不问就能做多少，MCP 工具怎么用。
- **先理解**：划定范围或调查改动时用哪些 skill。
- **子代理**：默认做法、并行、什么任务配什么模型、专门的工作流程。
- **文字和代码纪律**：原则、lint 工具、风格指南。
- **评审和验证**：复现的态度、验证 skill、现场测试工具。
- **流程**：git worktree、commit、PR、评审和合并用的工具。
- **Skill**：写 skill 的习惯、先修 skill、提议新 skill。

**poteto-mode** skill 是现成的样子。读它是为了把握粒度。不要照抄内容。用户的规则和 poteto-mode 的规则不一样。

### 4. 起草 skill

用 Cursor 内置的 `create-skill` skill 来写。放在哪里、怎么写：

- 路径：现有 mode skill 在哪个分类，就保留在哪。新建 mode 时，如果仓库里已经有这个名号的个人分类，用 `.cursor/skills/<handle>/<handle>-mode/SKILL.md`。否则默认放在项目里的 `.cursor/skills/<handle>-mode/SKILL.md`（用户更想要个人 skill 时，放 `~/.cursor/skills/<handle>-mode/`）。
- 名号：用户的名字，或用户自己选的标识。
- front matter 的 `description`：用用户的名字、`/<handle>-mode` 和「照 ta 的风格做事」来触发，不要用「write code」「review PR」这类泛泛的关键词。
- front matter 的格式：照 `create-skill` 的 YAML 规则。`description` 保持为一个 YAML 标量。标点或换行需要时，加引号，或者用 `description: >-` 加缩进的续行。
- front matter 默认写 `disable-model-invocation: true`。只有用户明确希望这个 mode 每一轮都生效时，才去掉。

### 5. 反复改文字

每一行都用 **unslop** skill 和 `create-skill` 的写作指南过一遍。

把草稿给用户看，听反馈。预计要改好几轮。下手要狠。mode skill 不是使用手册。

### 6. 合进去

在从 main 拉出的 worktree 里做。提交并开 PR。不要直接推到 main。

## 护栏

- **不要只凭一次对话下结论。** 说过一次、另一次又自相矛盾的偏好是噪声。要出现好几次才写进规则。
- **不要耍小聪明。** 复述别的 skill 的内容、发明比喻、给 agent 读者写「有诗意」的文字，只有成本没有好处。写成能照着做的。
- **引用，不要内嵌。** 用户依赖的其他 skill 用路径引用，不要贴摘录。用户在别处维护的原则文档也一样。
- **章节越少越好。** 只有用户在某方面有具体的、不同于默认的规则，才加这一节。「沟通要清楚」撑不起一节。「段落要短。比较选项时用表格。只有条目真正并列时才用列表。」才是。
- **规则写得通用。** 祈使句里写「用户」或「人」，不写作者的名字。
- **不要硬凑对称。** 如果用户没有值得写下来的流程规则，整节「流程」都不要。

## 评估

`-mode` skill 的产出是主观的。`create-skill` 那种测试、迭代、跑基准的循环在这里没用。跟用户一起凭感觉检查：读起来像不像 ta？有没有漏掉什么？然后交付。

只有 skill 在实际使用中真的触发不准，才去跑一轮优化 description 的循环。

## 什么时候别用

- 用户要的是做某件具体任务的 skill（不是工作习惯）：单独用 `create-skill`，不需要挖掘。
- 用户想记下一条很窄的工作流程（例如「我怎么写 commit message」）。那是普通 skill，不是 mode skill。
