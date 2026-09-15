---
title: Lunaverse IDE — 作品步骤、审查与完成状态
tags:
- lunaverse-ide
- calibration
created: '2026-09-15'
updated: '2026-09-15'
last_reviewed: '2026-09-15'
sources:
- raw/2026-09-15-lunaverse-ide-main-calibration.md
- raw/2026-09-15-lunaverse-ide-calibration-final-main.md
- raw/2026-09-15-lunaverse-ide-calibration-verification.md
status: current-source-snapshot
source_revision: 442fd53692d077c1e601231d79792c6c50b430e3
evidence_revision: 14c089322ba14c06b65fcb17d9ecdfd4d807b8d3
---

作品侧栏、Overview 与 Agent 上下文共用 shared 的步骤状态归约，而不是各自看文件或把模型活跃状态当进度。本页描述固定主线的交付判定；“当前任务完成”“整书步骤完成”“质量通过”和“已发布”是四种不同结论。

本页校准截至 main `442fd53692d077c1e601231d79792c6c50b430e3`；实现引用保留最初核对的 `14c089322ba14c06b65fcb17d9ecdfd4d807b8d3`。期间两次开发资料变更已核对，最终净差异仅删除三份本机开发 Skill，产品实现未变。版本与测试归属见 [[syntheses/lunaverse-ide-calibration-2026-09]]；这不是实时订阅。

## 三种显示状态

| 显示 | 意义 |
|---|---|
| Not started / 未开始 | 没有启动记录，也没有现役非空成果 |
| Processing / 进行中 | 步骤仍有未完成工作，不代表 Agent 此刻正在调用模型 |
| Done / 已完成 | 主体交付和机械要求的 reviewer 报告齐备，且没有明确未完成记录；仍不是自动质量认证 |

读取失败保留最近已知值并说明异常；没有已知值显示“—”，不能把读取错误伪装成未开始或完成。无 ledger 的旧书也可以从现有交付推导；损坏 ledger 不被静默覆盖。

## 数据与工具

书内 `.lunaverse/creator-step-progress.json` 与规范成果由 `readCreatorStepProgress` 共同归约。工具 `lunaverse_step_progress` 绑定会话原来的书，不跟随用户切换侧栏就改写另一本书。

| 参数 | 用途 |
|---|---|
| `action` | `read` 或 `update` |
| `expectedRevision` | update 带本次读取的 revision，防并发覆盖 |
| `updates` | 至少一项；每阶段最多一项 |
| `updates[].stage` | `source/novel-evaluation/character-bible/story-plan/scripts/assets`，不能用任意显示名代替 |
| `updates[].action` | `start` 或 `complete` |
| `updates[].scopeDescription` | 实际完成范围，区分局部任务与整个步骤 |
| `updates[].evidencePaths` | 可选，非空路径列表；受当前书/步骤约束 |
| `updates[].summary` | 当前结果说明 |
| `updates[].remaining` | 未完成范围或限制 |

实际开始返工再报 start；讨论、查询不改变 Done。完成并核对后报 complete，不强制先 start，不需要旧 reviewToken，不要求同一会话从头生成。可复用覆盖当前版本与范围的真实报告。

## Done 的机械交付要求

| 阶段 | 主体材料 | 规范 reviewer 材料 |
|---|---|---|
| 原文 | 现役非空原文或创作前提，排除 README/AGENTS/封面 | 没有统一独立 reviewer 文件要求 |
| 小说评估 | `01-novel-evaluator/evaluation-report.md`，代码兼容指定旧名称 | `01-novel-evaluator/review-report.md` |
| 角色 Bible | Bible 正文；声明的 bible_file 齐全 | `02-character-architect/bible-review-report.md` |
| 故事规划 | `03-entity-planner/00-structure-decision.md` 范围明确，每个规划节点有 Plan/Vault 成果 | `03-entity-planner/reviews/planner-review-report.md`，兼容根目录同名报告；若存在 rename_map 还需对应 apply_report |
| 剧本 | 规划范围每个节点都有唯一身份、基本结构有效的正式 LS | 每节点 `05-episode-writer/reviews/episode-review-{episode_id}.md`，`/`、`:` 转 `-`，Intro 用 episode-review-intro.md |
| 素材 | 当前正式脚本引用有登记交付路径，本地文件存在且非空 | 没有统一 reviewer 文件要求；生产和发布审核另算 |

不以空文件、任意报告或含 PASS 的文本凑 Done。工具的 `reviewReports` 与 `missingReviewReports` 列出具体证据/缺项；Agent 仍需读独立 reviewer 的真实结论、被审版本和未解决项。arc/sequence/path/playthrough/experience/rename 等条件审查按对应 Skill 判断，不由文件名猜测“全覆盖”。

## 局部目标与上游修改

正式计划定义全书范围，脚本按 `@episode` 去重。草稿、示例、备份、归档不计数；不凭空要求 intro 或某条主线。用户要求前三集时，可以当前任务完成而整书 Scripts 仍 Processing · 3/18；三集 demo 的完整范围可以是 3/3。

上游变动是影响分析提示，不自动让全部下游 Done 失效。Agent 判断哪部分确有未完工作再标 Processing。失败或中断留下半成品时保持 Processing；取消且旧成果完好时可核对后恢复 Done。

状态记账不授予写作、付费或发布权限；记账失败也不应阻塞无关的独立工作。登记远端 URL 不代表网络探测通过，质量与发布需要各自的证据。

相关：[[concepts/agent-manuals-agents-md]] · [[concepts/assets-produce-ide-workspace-contract]] · [[concepts/lunaverse-ide-release-and-operations]]。

## 核对来源

- [packages/shared/src/node/creator-step-progress.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/shared/src/node/creator-step-progress.ts)
- [packages/shared/src/creator-progress-presentation.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/shared/src/creator-progress-presentation.ts)
- [packages/ls-agent/src/creator-progress-tool.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-agent/src/creator-progress-tool.ts)
- [docs/runbooks/creator-step-progress.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/docs/runbooks/creator-step-progress.md)
- [packages/agent-runtime/src/lunaverse-system-prompt.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/agent-runtime/src/lunaverse-system-prompt.ts)
- [本次原始核对记录](../../raw/2026-09-15-lunaverse-ide-main-calibration.md)
- [补充验证与最终主线差异](../../raw/2026-09-15-lunaverse-ide-calibration-verification.md)
- [最终主线与开发指南撤回](../../raw/2026-09-15-lunaverse-ide-calibration-final-main.md)
