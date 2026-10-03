---
name: technical-writing
description: "分层技术写作标准：Diátaxis 结构、Google 开发者风格句子、STE 指令规则、Global English 句法。用于 /technical-writing，或撰写/审查文档、RFC、README、PR 描述、commit message 时。"
disable-model-invocation: true
---

# 技术写作

目标是疲惫工程师第一遍就能读懂。四层各答一问：这是什么文档、句子如何对读者说话、每句承载多少、有没有句能读成两种意思。四层都要用。

三层规则高于各层：

- **删掉不做工的每个字。** 句子里去掉某词仍成立，该词就删。「In order to」是「to」。「It is important to note that」整句删。
- **用短、日常词。** 「Use」不是「utilize」。「Help」不是「facilitate」。「Do」不是「perform」。长词须用精度换长度。
- **规则让句子更差时，换别的方式修句或别动。** 规则为读者服务。条条遵守却像机器写的句子是失败。

代码库即词表。写真实 symbol、文件、flag、命令名，不用同义词或描述性替换。

不要发明 jargon。用开发者会口头说的词：「move」「delete」「只减不增的 budget」，不是「evacuate」「ratchet」「endgame」。命名模式首次出现时说清含义即可。在回复里提议新 offender 及替换，作为 `unslop` abstract-metaphor 规则的增补 diff。不要改那个 skill。

## 变化节奏

各层决定文档说什么、每句承载多少。全遵守仍可能读起来像机器：句句过短、无处观点、无具体。

- 有意混合句长。短句落点。长句从容带条件或后果。
- 一句一思不等于句句同长。两句思想的句拆开。只承载一思的长句保留。
- 模式允许处有观点。Explanation 权衡利弊，说你怎么看，不要只列利弊。Reference 保持客观干述。
- 具体优于空泛。不是「schema 变更可能出问题」，而是「列 rename 会让 build 失败」。

## 先选模式（Diátaxis）

一文档一模式。两问：内容是促进行动（doing）还是理解（thinking）？服务学习还是工作？

- 行动 + 学习：**tutorial**。
- 行动 + 工作：**how-to**。
- 理解 + 工作：**reference**。
- 理解 + 学习：**explanation**。

compass 可用于整文档或单句。

**Tutorial：做中学。** 你是老师。学习者成功是你的责任。开头说会*建成*什么，不是会「学到」什么。每步早且常产生可见结果。告诉他们该看到什么：预期输出、prompt 变化、日志行。解释压到一从句加链接。教学停顿破坏课程。保持具体。用「我们」、命令式：「First, do x. Now, do y.」

**How-to：达成目标的步骤。** 解决人有的问题，不是机器能执行的操作。假设有能力。跳过教学。只有动作：无 digression、无背景、不为完整而完整。那些用链接。允许分叉与判断：「If you want x, do y.」指南以任务命名：「How to calibrate the radar array」，不是「Radar array calibration」。

**Reference：查阅用事实。** 描述。只描述。无指令、无说服、无观点。dry、完整、确定。陈述事实、选项、限制与错误，不 hedge。镜像被描述物的结构，使代码与文档可对照导航。材料放在读者预期处。能 codegen 则 codegen，保持真。

**Explanation：理解与原因。** 一个 bounded 主题，可脱离产品阅读。每个标题可容忍前面隐式「About...」。锚在真实 why 问题。给 context：设计决策、历史、约束、备选。此处允许观点，别处不允许。

不要混模式：tutorial 里无 reference 表，reference 里无 tutorial  hand-holding，how-to 里无争论。拆开链接。

## 句子对读者说话（Google developer style）

- 用「you」，现在时。「Will」只用于确实稍后才发生的事。
- 说谁做什么：「the compiler checks」，不是「is checked」。actor 未知或无关时被动才可。
- 指令用命令：「Click Submit.」事实 plain 陈述。Never「should be done」。
- 条件在指令前：「To delete the document, click Delete.」读者跳过不适用的。
- 常见情况先。例外在后。
- 像懂行的朋友。无 buzzword、无比喻语言、指令里无「please」，procedure 里 never「simply」「easy」「quickly」。真简单读者不会在这。
- 不 pre-announce（「we will soon support...」），不连续两句同开头。
- 链接用文字说明去向：页标题或短描述。Never「click here」。页内一句 context 优于链出去。
- 标题承载要点，不只话题（「先选模式」，不是「Modes」）。句首大写。任务标题是 bare 动词短语（「Create an instance」）。概念标题是名词短语。每页一个 h1，不跳级。
- 顺序用编号列表，其余 bullet。列表前用完整句引入。项保持 parallel。
- 代码用 code font。UI 元素 bold。用 serial comma。删「etc.」， upfront 说列表不完整。

## 陈述一次只加载一条（STE 规则）

- 每句一条指令。其余每句一思。
- 指令长约超 20 词、其他句长约超 25 词则拆。
- 警告或条件在所护步骤前：「If hot oil touches your skin, injuries can occur.」
- 保留「the」「a」：「Remove backup file」读两种。「Remove the backup file」读一种。
- 每词一义一职，然后保持。若「check」是 inspect，别处不用表 restrain。
- 每动作一词并坚持：「start」，不要此处 start 彼处 initiate。
- 程序用直接命令，不用叙述、不用被动：「Install the component」，不是「the component must be installed」。
- 能避则避「-ing」词。语法职务太多，易误读。

## 不留双读句（Global English）

- 「only」「not」等紧贴所修饰词：「only fails on growth」与「fails only on growth」不同。
- 拆长名词串：「the proto import budget check script」→「the script that checks the proto-import budget」。
- 每个「it」「they」「this」指向明显一物。疑则重复名词。Never 用「this」「which」指整句。
- 不丢动词：「Phase 1 moves the converters and Phase 2 the runtime」Phase 2 缺动词。补上。
- 保留表结构的小词。「Ensure that the switch is off」保留「that」使只 parse 一种。不为字数牺牲清晰。
- 系列中重复冠词防误读：「the client and the host」，不是「the client and host」，当指两物时。
- 句可两种 grouping 时说清「and」「or」连什么。「Both...and」「either...or」「if...then」是免费 disambiguator。
- 用句号，不用分号。em dash 换新句。
- 括号内是完整语法单位或独立句。Never 用「(s)」造复数。
- 无斜杠：写「a, b, or both」，不写「a/b」或「and/or」。
- 一物一名 everywhere。同一物说「the gate」「the ratchet」「the budget check」等于教三物。未改句 between  edits 也同代价。未改的不 churn。
- 跳过 idiom、口语、拉丁缩写、隐喻。非母语读者、译者与 agent 都最擅长 plain 构造。

## 语气与仓库细则

- 本 skill 触及的每份文档应用 **unslop** skill。该 skill 拥有 slop 模式目录：AI 词汇、filler、hedge、格式 tell。
- PR 描述与 commit message 也是写作。除 Diátaxis 外各层均适用。PR 正文是一分钟内能读完的 briefing。不要粘贴 swarm 日志、SHA 列表或 metric 表。链接它们。
- 产品 UI 字符串不是文档。那些用产品 copy 指南。
- 代码 snippet 用 tab 缩进。写真实路径与 symbol。每个 count 或 tree 声称在 landing commit 为真，并含再生命令。

## 示例

Before:

> Configuration of the proto import ratchet budget script parameters is performed via budget.json. Note that it's important to remember that running with --write, which updates the committed budget to reflect the current count, should only be done when lowering it. If exceeded, CI fails.

After:

> `budget.mjs` reads the committed budget from `budget.json` and counts the files that import protos. If the count exceeds the budget, CI fails. Run `budget.mjs --write` only to lower the budget.
