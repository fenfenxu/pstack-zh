# Linear 工单

## 这个来源里有什么

- 描述功能、缺陷及其动机的 issue
- 附在 issue 上的项目文档（常为 PRD 或规格）
- 父/子 issue 关系（更大计划 → 具体工单）
- issue 评论（澄清、范围变更、「为何做」的理由）
- 标签（如 `compliance`、`customer-request`、`perf`），标示动机类型
- 解释范围变更的状态更新
- 附件与链接的 GitHub PR

Linear 常承载产品/业务上下文：「因客户 X 要求而做」或「这是 Q3 合规计划」那一层。

## 怎么搜

使用 Linear MCP。

1. **从链接工单开始。** 若种子提交或 PR 引用工单 ID（如 `ENG-1234`、`[BUG-567]`），先用 `get_issue` 拉取。读完整 issue 含评论。
2. **按关键词列相关 issue。** 用 `list_issues` 文本搜索功能名、关键符号或业务术语。试多种措辞。
3. **遍历 issue 树。** 若落到子 issue，拉取父 issue。子 issue 偏战术，父 issue 常带「为什么」。
4. **读项目文档。** 若 issue 属某项目，用 `get_project` 查附件文档。项目级文档最常记录规格与理由。
5. **查标签与里程碑。** 标签暗示动机类别（customer-request、incident-followup、compliance）。里程碑关联 截止日期，常揭示动机。

## 这里怎样算好证据

- issue 描述陈述业务问题：「客户 Acme 因 SOC2 审计需要 X」
- 评论记录决策：「我们选方案 B，因方案 A 需动 billing 服务」
- 父 issue 标题像计划：「Q3 Enterprise Readiness」或「Reduce Payment Failures」
- 附 PRD 或规格
- 标签如 `customer:acme`、`incident-followup`、`compliance`、`perf-regression`

## 常见陷阱

- **范围漂移。** PR 引用的 ticket 可能已关闭并以不同范围重开。读完整历史。
- **机械模板。** 有些团队要求填「Why」但用套话。泛化文案（「提升用户体验」）可能不是真答案。
- **陈旧 ticket。** 旧 ticket 常反映已变的计划版本。核对日期并与代码发布日期交叉验证。
- **以重复关闭的链。** 沿 重复于 关系回到主工单。
- **私有工作区内容。** 若无法访问 issue，记为缺口而非猜测。

## 要返回什么

每个相关 ticket：
- 工单 ID 与标题
- 从描述或评论引用的 问题/动机（不要意译。合成器需要确切文本以引用）
- 标签、父 issue、项目
- 作者、创建日期、关闭日期
- 链接（若有）
