### Eval

**你拥有实验设计。规划、盲测、运行、综合。**

**盲测不可协商项：**

- candidate 可见的任何目录、文件或 prompt 中不得出现 `eval`、`test`、`judge`、`experiment`、`rubric`、`score`、`compare`、`benchmark`、`candidate` 或 `arena`。
- candidate prompt 像有机用户请求。陈述目标，非 meta。
- 无 chain-eliciting cue。不要问 candidate 列出用了哪些 skill、principle 或文件。一般性要设计说明，从代码形状而非自报 grade chain-following。
- sanitize 目录与 slug 名。用用户可能选的项目形名称。
- 不要告诉 candidate 还有其他 candidate。
- judge 可知自己在 judge，但仅按 sanitized label 看输出，never 按 model 名。
- 比较两变体：一个 judge 在同一 scale 上单 pass 给两套打分，盲于每套来源。

**步骤：**

1. **Frame.** 陈述被测变体与成功行为。为 judge 写 rubric（3–6 条具体标准）。 withhold 于 candidate。
2. **Set up sanitized environments.** 每 candidate 工作 dir 放置变体。植入有机任务会有的 context：项目 skeleton、candidate 会自然读的 skill。
3. **Author one organic prompt.** 用户会打的字。无泄漏测量内容。
4. **Spawn N parallel candidates** 按 **arena** skill Phase B 用不同 model。各在 sanitized dir 工作。同一 prompt。
5. **Spawn one blinded judge** 按 **arena** skill Phase C 用不同 model 族。judge 见 sanitized label 与 rubric，never model 名。
6. **Verify the chain from transcripts, not self-report.** 读 active workspace `agent-transcripts/` 下各 candidate 本地 transcript（系统 prompt 命名此路径）。不要 glob `~/.cursor/projects/*/`。那会跨 workspace 读无关项目 private chat。看各 candidate 实际打开的文件。从真实读过的文件加代码形状 grade chain-following，never 自报。
7. **Read every candidate output yourself** 端到端。与 judge verdict 比较。分歧意味着 model biased 或 rubric ambiguous。综合。

**Reply：** 被测变体、rubric、每 candidate 笔记、judge verdict、你的综合、是否 promote 变体的建议。
