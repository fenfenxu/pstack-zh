---
name: swarm
description: "扇出 N 个并行 worker，收齐后返回一份报告。用于 /swarm、「swarm this」，或并行覆盖、race、gauntlet 与探索。"
disable-model-invocation: true
---

# Swarm

扇出 N 个并行 cloud worker。可覆盖不同 slice、对同一 brief race，或混合。父 agent 等待、聚合、返回一份报告。

## 开始

启动任何东西前先打开 todolist，每阶段一条。

1. 定框
2. 扇出
3. 聚合
4. 报告

## 阶段 A：定框

1. 说明完成谓词与 swarm 须返回的 artifact 或报告。
2. 选形态。分 slice、N worker 同 brief race，或混合。race 或混合形态须在 spawn 前声明 `first pass`、`rank all` 或 `best-of`。
3. N 来自用户或从形态推导。N 是 worker 总数，不是 cloud 并发上限。
4. worker 模型取自 `~/.cursor/rules/pstack-models.mdc` 的 `swarm workers` 行。规则或该行缺失时用 `grok-4.7-xhigh-fast`。`auto` 或 `inherit-parent` 则省略 `model`，worker 跑在父模型上。Task 拒绝 slug 则用默认并说明。默认也被拒则从错误信息取同族最接近有效 slug。model race 须 upfront 命名各 arm 模型。
5. 写输出的 worker 各用可写输出路径。worker 验证或度量 commit 时，各 brief 命名确切 SHA。度量 brief 还命名方法（样本数、一样本是什么、顺序）。worker 在结果中记录二者。

## 阶段 B：扇出

一条消息 spawn 全部 N worker：`subagent_type: generalPurpose`、`environment: "cloud"`、`run_in_background: true`、步骤 4 的 model（`auto` 或 `inherit-parent` 时不设）。仅当 worker 需访问用户电脑上的东西时用 `environment: "local"`。

worker 须从非默认已 push 分支开始时，传 `cloud_base_branch`。

每条 brief 自洽。含目标、范围、确切 slice 或 race arm、如何验证、报告什么。报告用 `PASS`、`ISSUES` 或 `BLOCKED` 加证据。能证明缺陷的 worker 报 `ISSUES` 并列出能证明的全部 issue，不只第一个。

某 worker dropout，以 N-1 继续并注明。

## 阶段 C：聚合

读 terminal 结果。未记录 brief 要求的 SHA 与方法的结果丢弃，重新启动一个新 worker 一次。第二次仍 miss 则记 gap。gap 不算 pass。覆盖形态下每个必需 slice 都要有结果。race 按 upfront 声明的选择规则：`first pass`、`rank all` 或 `best-of`。不要粘贴 raw worker dump。

保留紧凑结果表、一行 evidenced issue、明确 gap 或 dropout。

## 阶段 D：报告

在 chat 内返回一份合并报告：表、issue 一行摘要、gap 或 dropout、若用了 race 规则则写明。
