# pstack Skills 中文索引

共 **49** 个 skill。日常用法：先 `/setup-pstack`，干活用 `/poteto-mode`；其余多数由 mode 按需调用，也可直接 `/skill-name`。

想按课表练，从 [教程](../tutorials/index.md) 读起。本页是 reference 查表。

skills 与 agents 的文件都在这个仓库里。模型表由 `/setup-pstack` 写在你本机，不在这里。

---

## 怎么选

| 你想做的事 | 用这个 |
|---|---|
| 配置各角色用哪款模型 | [`/setup-pstack`](./setup-pstack/SKILL.md) |
| 非琐碎工程任务（默认入口） | [`/poteto-mode`](./poteto-mode/SKILL.md) |
| 子系统**怎么**跑 | [`/how`](./how/SKILL.md) |
| 设计**为什么**这样 | [`/why`](./why/SKILL.md) |
| 真正搞懂（how + why） | [`/teach`](./teach/SKILL.md) |
| 续上进度 / 我做到哪了 | [`/recall`](./recall/SKILL.md) |
| 这点改动会不会炸别处 | [`/blast-radius`](./blast-radius/SKILL.md) |
| 写代码前先定类型与模块形 | [`/architect`](./architect/SKILL.md) |
| 同一题并行 N 案再嫁接 | [`/arena`](./arena/SKILL.md) |
| 不同切片并行再汇总 | [`/swarm`](./swarm/SKILL.md) |
| 多模型挑刺评审 diff | [`/interrogate`](./interrogate/SKILL.md) |
| 没有合适 playbook | [`/figure-it-out`](./figure-it-out/SKILL.md) |
| 无人值守要可审查决策日志 | [`/show-me-your-work`](./show-me-your-work/SKILL.md) |

---

## 1. 入口与配置

| Skill | 一句话 |
|---|---|
| [`poteto-mode`](./poteto-mode/SKILL.md) | 主模式：匹配 playbook、路由其他 skill、去 slop、可验证交付 |
| [`setup-pstack`](./setup-pstack/SKILL.md) | 按角色选模型与推理预算，写入始终生效规则 |

---

## 2. 理解与设计

| Skill | 一句话 |
|---|---|
| [`how`](./how/SKILL.md) | 走读架构、运行时流程、该放哪一层 |
| [`why`](./why/SKILL.md) | 并行查 MCP 证据（git/工单/文档/聊天/可观测性等），解释决策与权衡 |
| [`teach`](./teach/SKILL.md) | 跑 how + why，织成平实、可跟进的说明 |
| [`recall`](./recall/SKILL.md) | 从聊天史与共享记录重建「当前状态」简报 |
| [`architect`](./architect/SKILL.md) | 实现前先定签名、类型、模块边界 |
| [`blast-radius`](./blast-radius/SKILL.md) | 用真实运行证明「安全所依赖的那条事实」 |

---

## 3. 并行与评审

| Skill | 一句话 |
|---|---|
| [`arena`](./arena/SKILL.md) | N 个候选 → 选基底 → 嫁接其余长处 |
| [`swarm`](./swarm/SKILL.md) | N 个 worker 分片/竞速 → 一份聚合报告 |
| [`interrogate`](./interrogate/SKILL.md) | 多模型对抗评审，含严格代码质量视角 |
| [`reflect`](./reflect/SKILL.md) | 从本次对话沉淀教训，路由到具体 skill 编辑 |

---

## 4. 验证与质量

| Skill | 一句话 |
|---|---|
| [`tdd`](./tdd/SKILL.md) | 明确要求 TDD / 有便宜本地测试目标时：先红后绿 |
| [`create-verification-skill`](./create-verification-skill/SKILL.md) | 生成项目本地「像用户一样驱动应用」的 verify skill |
| [`maintain-verification-skill`](./maintain-verification-skill/SKILL.md) | 审计并修正 verify skill / feature map 漂移 |
| [`no-comments`](./no-comments/SKILL.md) | 拉起 Comment Sicko，修接受项，约束编码化 |
| [`typescript-best-practices`](./typescript-best-practices/SKILL.md) | 读/写 `.ts`/`.tsx` 时的 TS 实践（类型纪律落地） |
| [`benchmark-checklist`](./benchmark-checklist/SKILL.md) | 汇报测到的提速或退步之前，先核对这个数字 |
| [`unslop`](./unslop/SKILL.md) | 剔除 AI 写作痕迹（写作侧常驻） |
| [`technical-writing`](./technical-writing/SKILL.md) | Diátaxis + Google 风格 + STE 等分层文档标准 |

---

## 5. 自主与元工作流

| Skill | 一句话 |
|---|---|
| [`figure-it-out`](./figure-it-out/SKILL.md) | 无窄 playbook 时，现场设计可审计 playbook |
| [`show-me-your-work`](./show-me-your-work/SKILL.md) | TSV 决策轨迹（做了什么 / 为何 / 证据 / 结果） |
| [`automate-me`](./automate-me/SKILL.md) | 按你的真实工作方式起草个人 `-mode` skill |
| [`make-bot-ui`](./make-bot-ui/SKILL.md) | 做通过 webhook 唤醒 Grok Bot 的自定义 UI |
| [`bro`](./bro/SKILL.md) | 用白话重述上一条消息，去掉术语 |

---

## 6. 设计原则（`principle-*`，24 条）

由 poteto-mode / 其他 skill 按情境调用；一般不直接当入口。

### 思考与前提

| Skill | 一句话 |
|---|---|
| [`principle-foundational-thinking`](./principle-foundational-thinking/SKILL.md) | 先把核心数据结构做对 |
| [`principle-attack-the-premise`](./principle-attack-the-premise/SKILL.md) | 共享前提的多次修复都失败 → 先质疑前提 |
| [`principle-redesign-from-first-principles`](./principle-redesign-from-first-principles/SKILL.md) | 新需求当 foundational，不要 bolt-on |
| [`principle-exhaust-the-design-space`](./principle-exhaust-the-design-space/SKILL.md) | 无先例决策前做 2–3 个竞争原型 |
| [`principle-fix-root-causes`](./principle-fix-root-causes/SKILL.md) | 追到根因再修，别用守卫糊症状 |
| [`principle-experience-first`](./principle-experience-first/SKILL.md) | 用户体验优先于实现便利 |

### 形状与边界

| Skill | 一句话 |
|---|---|
| [`principle-model-the-domain`](./principle-model-the-domain/SKILL.md) | 用结构编码领域，而非散落 if |
| [`principle-boundary-discipline`](./principle-boundary-discipline/SKILL.md) | 守卫放在边界；内部信类型 |
| [`principle-type-system-discipline`](./principle-type-system-discipline/SKILL.md) | 非法状态不可表示；边界解析外部数据 |
| [`principle-minimize-reader-load`](./principle-minimize-reader-load/SKILL.md) | 减少跟踪层数与脑中隐状态 |
| [`principle-separate-before-serializing-shared-state`](./principle-separate-before-serializing-shared-state/SKILL.md) | 并发先消共享，再谈串行化 |

### 变更策略

| Skill | 一句话 |
|---|---|
| [`principle-subtract-before-you-add`](./principle-subtract-before-you-add/SKILL.md) | 先删死代码/冗余，再在更简基座上加 |
| [`principle-laziness-protocol`](./principle-laziness-protocol/SKILL.md) | 偏向删除与能解决问题的最小变更 |
| [`principle-migrate-callers-then-delete-legacy-apis`](./principle-migrate-callers-then-delete-legacy-apis/SKILL.md) | 同波次迁 caller 并删旧 API |
| [`principle-outcome-oriented-execution`](./principle-outcome-oriented-execution/SKILL.md) | 收敛目标架构，别堆可丢弃兼容层 |
| [`principle-sequence-verifiable-units`](./principle-sequence-verifiable-units/SKILL.md) | 拆成可验证小单元再堆叠提交 |
| [`principle-make-operations-idempotent`](./principle-make-operations-idempotent/SKILL.md) | 崩溃/重试后仍收敛同一终态 |

### Agent 执行

| Skill | 一句话 |
|---|---|
| [`principle-prove-it-works`](./principle-prove-it-works/SKILL.md) | 对真实产物验证，不靠「能编译」 |
| [`principle-explain-the-number`](./principle-explain-the-number/SKILL.md) | 相信或汇报测到的数字之前，先说出限制因素 |
| [`principle-test-behavior-not-implementation`](./principle-test-behavior-not-implementation/SKILL.md) | 像用户一样测可观察结果 |
| [`principle-build-the-lever`](./principle-build-the-lever/SKILL.md) | 建可重复运行的工具/skill，少手工扫 |
| [`principle-encode-lessons-in-structure`](./principle-encode-lessons-in-structure/SKILL.md) | 复发教训 → lint/检查/脚本，而非更多文字 |
| [`principle-guard-the-context-window`](./principle-guard-the-context-window/SKILL.md) | 大块给子代理；主线程只留摘要 |
| [`principle-never-block-on-the-human`](./principle-never-block-on-the-human/SKILL.md) | 可逆事先做再展示；仅不可逆才确认 |

---

## 7. poteto-mode 的 23 个 Playbook

路径：[`poteto-mode/playbooks/`](./poteto-mode/playbooks/)

| Playbook | 适用 |
|---|---|
| [investigation](./poteto-mode/playbooks/investigation.md) | 只读问题：怎么工作、为何如此、是否确定 |
| [bug-fix](./poteto-mode/playbooks/bug-fix.md) | 复现 → 根因 → 带运行时证据的修复 |
| [perf-issue](./poteto-mode/playbooks/perf-issue.md) | 相对基线诊断并改进可测的慢 |
| [hillclimb](./poteto-mode/playbooks/hillclimb.md) | 对单一指标持续科学爬坡，一赢一 commit |
| [runtime-forensics](./poteto-mode/playbooks/runtime-forensics.md) | 从埋点诊断现场症状（泄漏、空转 CPU 等） |
| [trace-forensics](./poteto-mode/playbooks/trace-forensics.md) | 分析已捕获的 profile/trace/spindump/heap |
| [feature](./poteto-mode/playbooks/feature.md) | 新/改行为，从命名数据形态出发 |
| [refactoring](./poteto-mode/playbooks/refactoring.md) | 保行为的结构/形态变更 |
| [prototype](./poteto-mode/playbooks/prototype.md) | 低成本草图，或 empirically 比岔路 |
| [visual-parity](./poteto-mode/playbooks/visual-parity.md) | 两套实现像素级 UI 对齐 |
| [authoring-a-skill](./poteto-mode/playbooks/authoring-a-skill.md) | 写/改 SKILL.md |
| [eval](./poteto-mode/playbooks/eval.md) | 盲测 skill/prompt 变更对行为的影响 |
| [babysit](./poteto-mode/playbooks/babysit.md) | 推 PR/栈到可合并：冲突、评审、CI |
| [shipping](./poteto-mode/playbooks/shipping.md) | 独立验证绿灯栈后自下而上落地 |
| [autonomous-run](./poteto-mode/playbooks/autonomous-run.md) | 长任务不停顿推到完成 |
| [orchestrate](./poteto-mode/playbooks/orchestrate.md) | 多日、多 PR、多子代理的协调者会话 |
| [autopilot-full](./poteto-mode/playbooks/autopilot-full.md) | 独立 PR 各自跑到合并，轮次 swarm 裁决 |
| [autopilot-stack](./poteto-mode/playbooks/autopilot-stack.md) | 建并验证线性栈，给人审后落地 |
| [session-pickup](./poteto-mode/playbooks/session-pickup.md) | 接手/恢复先前 agent 进行中的工作 |
| [pause-safely](./poteto-mode/playbooks/pause-safely.md) | 干净挂起，便于日后恢复 |
| [multi-phase-plan](./poteto-mode/playbooks/multi-phase-plan.md) | 跨阶段或堆叠 PR 的规划 |
| [worktree-cleanup](./poteto-mode/playbooks/worktree-cleanup.md) | 安全清理已合并/废弃 worktree 等 |
| [opening-a-pr](./poteto-mode/playbooks/opening-a-pr.md) | 小有序 commit → 常规标题 + briefing 正文（几乎每个 playbook 收尾都会用） |

---

## 字母序速查

| 目录名 | 中文标签 |
|---|---|
| [`architect`](./architect/SKILL.md) | 架构先行 |
| [`arena`](./arena/SKILL.md) | 竞技场嫁接 |
| [`automate-me`](./automate-me/SKILL.md) | 个性化 mode |
| [`blast-radius`](./blast-radius/SKILL.md) | 爆炸半径 |
| [`bro`](./bro/SKILL.md) | 白话重述 |
| [`create-verification-skill`](./create-verification-skill/SKILL.md) | 创建验证 skill |
| [`figure-it-out`](./figure-it-out/SKILL.md) | 现场编 playbook |
| [`how`](./how/SKILL.md) | 如何工作 |
| [`interrogate`](./interrogate/SKILL.md) | 对抗评审 |
| [`maintain-verification-skill`](./maintain-verification-skill/SKILL.md) | 维护验证 skill |
| [`make-bot-ui`](./make-bot-ui/SKILL.md) | Bot UI |
| [`no-comments`](./no-comments/SKILL.md) | 去注释 |
| [`poteto-mode`](./poteto-mode/SKILL.md) | 主模式 |
| [`principle-*`](./INDEX.md#6-设计原则principle--22-条) | 22 条原则（见上） |
| [`recall`](./recall/SKILL.md) | 回忆上下文 |
| [`reflect`](./reflect/SKILL.md) | 反思沉淀 |
| [`setup-pstack`](./setup-pstack/SKILL.md) | 模型配置 |
| [`show-me-your-work`](./show-me-your-work/SKILL.md) | 展示决策轨迹 |
| [`swarm`](./swarm/SKILL.md) | 蜂群并行 |
| [`tdd`](./tdd/SKILL.md) | 测试驱动修 bug |
| [`teach`](./teach/SKILL.md) | 教会我 |
| [`technical-writing`](./technical-writing/SKILL.md) | 技术写作 |
| [`typescript-best-practices`](./typescript-best-practices/SKILL.md) | TS 最佳实践 |
| [`unslop`](./unslop/SKILL.md) | 去 AI 腔 |
| [`why`](./why/SKILL.md) | 为何如此 |

英文原文总览见上游 [`pstack/README.md`](https://github.com/cursor/plugins/blob/e43c7ee26e0038c6c1fa8380dd34ce86ff94cb2a/pstack/README.md)。
