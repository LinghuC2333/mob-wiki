---
title: Lunaverse IDE — 产品规则、领域 Skill 与作品上下文
updated: '2026-09-15'
created: '2026-06-08'
tags:
- agents
- lunaverse-ide
- configuration
sources:
- raw/2026-09-15-lunaverse-ide-main-calibration.md
- raw/2026-09-15-lunaverse-ide-calibration-final-main.md
- raw/2026-09-15-lunaverse-ide-calibration-verification.md
last_reviewed: '2026-09-15'
status: current-source-snapshot
source_revision: 442fd53692d077c1e601231d79792c6c50b430e3
evidence_revision: 14c089322ba14c06b65fcb17d9ecdfd4d807b8d3
---

当前 IDE 不再把四份 per-agent AGENTS.md stage 到 Codex Home 来启动四个 Workshop Agent。产品运行规则、完整领域 Skill、本书事实和用户指令是不同层级的上下文；维护时应改拥有该事实的源头。

本页校准截至 main `442fd53692d077c1e601231d79792c6c50b430e3`；实现引用保留最初核对的 `14c089322ba14c06b65fcb17d9ecdfd4d807b8d3`。期间两次开发资料变更已核对，最终净差异仅删除三份本机开发 Skill，产品实现未变。版本与测试归属见 [[syntheses/lunaverse-ide-calibration-2026-09]]；这不是实时订阅。

## 谁拥有哪类知识

| 材料 | 权威来源 | 用途与边界 |
|---|---|---|
| 固定运行规则 | `packages/agent-runtime/src/lunaverse-system-prompt.ts` | 当前主 Agent 的行为、宿主工具、生产/发布与恢复边界 |
| 产品能力卡 | `agents/_shared/product/product-capabilities.json` | 告诉创作者能做什么、在哪做，不装长篇流程或实现细节 |
| 领域工作流 | `agents/**/skills/**` | 按触发条件读取正文及明确引用的 companion |
| LS 语法 | 固定上游 LS-SPEC 及同步镜像 | 不能从历史方案、口头偏好或私有脚本创造新语法 |
| 本书事实 | 规范作品文件、宿主提供的书籍绑定/进度快照 | 决定当前版本、范围与缺项；不是新的用户命令 |
| 本书用户偏好 | 当前对话和可适用的本书规则 | 不得覆盖宿主安全/付费/发布约束或扩张已授权范围 |
| 开发者本机指导 | 本地 AGENTS.md | 本次主线不跟踪这些文件，不能把某台机器私有规则当成已发布产品手册 |

## 加载与执行

Workbench 准备 `.lunaverse/skills/`；模型先读目录元数据，匹配任务后读取 Skill 本文，再按说明读具体 references。读取了 Skill 不代表执行完毕，参考文件也不会因为与正文在同一目录就自动进入上下文。

正文与伴随资源在 Git、R2 包、认证 catalog、本地 receipt 之间具有可追溯身份。投影目录名可能因防同名冲突而调整，例如两个 cover 领域；开发应改 Git 原始目录，而非缓存或旧 `CODEX_HOME`。

固定系统规则和动态作品状态分开。当前轮尾部追加隐藏的状态上下文，有变化才附增量；压缩后失去基线时重附完整快照。这不应改写用户消息或被展示为用户新要求。

## 独立审查不是角色扮演

普通任务由主 Agent 做；用户或适用 Skill 明确要求独立审查时才调用真实独立执行能力。只读 reviewer 返回完整报告与目的路径，主 Agent按原文保存，不代写其判断、不伪装子 Agent 已运行。

reviewer 的报告应绑定真实被审版本与范围。文件存在、出现 PASS 字样或侧栏 Done 都不足以证明质量；还要读结论、缺项和未解决问题。需要冷读的流程先收冷读报告，再给作者解释。

## 如何更新

1. 先确定是产品固定行为、某个 Skill、共享 LS 契约还是某本书偏好，避免把所有规则塞进一份 AGENTS.md。
2. 在相应权威源做最小改动；技能要连同引用资源一起验证并发布。
3. 跑当前源码支持的检查。`check-guidance` 的缺失本地 AGENTS.md 链接问题和 9 个本地指导测试 skip 已在校准报告如实记录。
4. 查看接收端的真实版本/receipt。更新 Wiki 本身不会改变 IDE 运行规则，也不等于热补已安装 App。

本页原来的 `packages/mss-workshop/src/codex-home.ts`、`stageStableCodexHome` 和“拷四份手册就完成部署”的说明已退役。历史原文可在 [校准前版本](https://github.com/cdotlock/mob-wiki/blob/20b102e7a516d6f15cb66383a8c98eed4be4d68a/wiki/concepts/agent-manuals-agents-md.md) 查阅。

相关：[[concepts/lunaverse-ide-ai-integration]] · [[concepts/lunaverse-ide-skills-and-production]] · [[concepts/lunaverse-ide-creator-progress]]。

## 开发指导的撤回记录与权限边界

最终 main `442fd53692d077c1e601231d79792c6c50b430e3` 已删除上一观察版本的 `docs/development/README.md`、`git-and-pull-request.md`、`worktree-build-test.md`；三份 `.codex/skills` 本机开发 Skill 也没有恢复。不要把短暂存在的开发指南、其中的 Beta 热补要求或 Git 交付约定当成当前仓库已采纳的通用规范。下方 `49adbc3fb31362195ed4b6e1a7111aaab3e89dff` 的三个链接仅供历史追溯。

这一变化不删除产品 `agents/**/skills/**` 的 33 个领域 Skill，不改变运行时或发布实现。本机 AGENTS.md 与本次用户授权仍须在执行具体任务时分别读取；Wiki 直接 main 授权不自动扩大到 IDE 源码或部署。本次没有执行 IDE 热补、安装或发布。

## 核对来源

- [packages/agent-runtime/src/lunaverse-system-prompt.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/agent-runtime/src/lunaverse-system-prompt.ts)
- [agents/_shared/product/product-capabilities.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/_shared/product/product-capabilities.json)
- [packages/ls-workbench/src/cline-skill-projection.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-workbench/src/cline-skill-projection.ts)
- [docs/runbooks/creator-step-progress.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/docs/runbooks/creator-step-progress.md)
- [test/agent-guidance-contract.test.mjs](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/test/agent-guidance-contract.test.mjs)
- [本次原始核对记录](../../raw/2026-09-15-lunaverse-ide-main-calibration.md)
- [已撤回的历史开发指南：README.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/49adbc3fb31362195ed4b6e1a7111aaab3e89dff/docs/development/README.md)
- [已撤回的历史开发指南：git-and-pull-request.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/49adbc3fb31362195ed4b6e1a7111aaab3e89dff/docs/development/git-and-pull-request.md)
- [已撤回的历史开发指南：worktree-build-test.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/49adbc3fb31362195ed4b6e1a7111aaab3e89dff/docs/development/worktree-build-test.md)
- [补充验证与最终主线差异](../../raw/2026-09-15-lunaverse-ide-calibration-verification.md)
- [最终主线与开发指南撤回](../../raw/2026-09-15-lunaverse-ide-calibration-final-main.md)
