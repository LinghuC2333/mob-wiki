---
title: Lunaverse IDE Wiki 整体校准 — 2026-09-15
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

本次对照 IDE 权威远端 main 的固定提交校准知识库，目标是让当前操作知识、历史依据和未验证状态彼此可区分。覆盖不是一句“全部看过”：校准附录逐项列出了校准前 87 个 Wiki 页面的处理决定，以及源仓模块、命令、设置、技能、CLI、工作流和已发现的缺口。

本页校准截至 main `442fd53692d077c1e601231d79792c6c50b430e3`；实现引用保留最初核对的 `14c089322ba14c06b65fcb17d9ecdfd4d807b8d3`。期间两次开发资料变更已核对，最终净差异仅删除三份本机开发 Skill，产品实现未变。版本与测试归属见 [[syntheses/lunaverse-ide-calibration-2026-09]]；这不是实时订阅。

## 基线与范围

- Wiki 起点：`20b102e7a516d6f15cb66383a8c98eed4be4d68a`。
- IDE 初始证据：`MobAI-Inc/lunaverse-ide@14c089322ba14c06b65fcb17d9ecdfd4d807b8d3`，main 提交时间 `2026-09-15T12:20:51+08:00`。
- 最终校准 main：`442fd53692d077c1e601231d79792c6c50b430e3`，提交时间 `2026-09-15T13:15:44+08:00`。中间版本 `49adbc3fb31362195ed4b6e1a7111aaab3e89dff` 曾新增三份 docs/development 共享指南，最终提交又将其删除；本次已阅读两次完整差异。相对初始证据的净差异仅删除三份本机 .codex/skills，产品实现/依赖/测试输入未变。
- 原本地 IDE 有未提交编辑和草稿目录；为免将个人未合入内容误记为团队现状，本次另建 detached 固定副本，未修改原工作区。
- 被消费 LS 上游：`cdotlock/lunascripts@515a9e3bf677e033d069a56038066ddf9b47adf5`，契约 `4.0.0`。
- 本次是 Wiki 的整体事实校准，不是 IDE 全源代码安全审计，也不是各独立后端仓库或生产环境的完整验收。

## 主要纠偏

| 原知识容易误导的地方 | 校准后的现状 |
|---|---|
| 旧 cdotlock IDE 仓是主线 | MobAI-Inc 为主；镜像/独立 Cloud 仓分开 |
| Cline + Codex 0.130/shim 为当前双运行时 | Pi 为唯一产品 Agent runtime；Tab 单轮 ChatBackend 保留 |
| 8 packages / 6 Agents 等同产品结构 | 11 个顶层 package、8 个内置扩展；领域 namespace 不等于运行进程 |
| 四份 per-agent AGENTS.md stage 即是现行手册 | 固定系统规则 + Skill 完整包 + 产品能力卡 + 书籍事实分层 |
| Langfuse 是 Skill 当前发布权威 | Git → R2 不可变包 → 认证网关 → receipt-valid 本地投影 |
| 16 个远端 style family 是全部新选择 | 新选择受三个完整随包 recipe 管控；网关/Langfuse 兼容面仍存在 |
| mapping.json 是唯一事实，IDE 不扫描 | 当前有规范本地 mapping 与磁盘发现/补全；文件存在性须核实 |
| 根目录 .md 脚本/旧 numbered 路径可继续新写 | books/<id> 规范目录与正式 .ls；旧路径仅按代码兼容 |
| 绿幕 matting/upscale 是当前角色必需链 | 注册表已移除旧 remote 原子，当前透明角色原生 2K alpha + 本地收尾 |
| 固定 18/25 atom 表可直接调用 | 源码注册 23 项；实例/禁用项/schema/宿主准入另行决定可用性 |
| 普通媒体直接 CLI/任意后台跑 | 先 lunaverse_produce；专家例外需明确 providerStarted=false 移交 |
| 六月 LS / YOU 清屏 / 小写作者 signal | LS 4.0、INNER_THOUGHT 显示 MC、作者 signal 全大写；消费者保留版本兼容 |
| submit pending 后只能旧 admin activate | IDE JWT + Cloud workflow、readiness、fenced activation 和 runtime truth |
| Done、Agent 已停、preview 成功可代表发布 | 四种独立状态；增加完整工程快照与审查/进度说明 |

## 源码检查证据

在隔离副本用 Node 22 与 pnpm 10.26.2 按 lockfile 离线安装依赖，忽略 lifecycle scripts；未做完整应用构建或运行付费场景。

| 检查 | 实际结果 | 能证明/不能证明 |
|---|---|---|
| repository migration guard | 通过 | 所扫描发布/脚本面没有未批准旧 IDE 仓引用；不是所有 prose 已同步 |
| LS authority（离线） | 通过 | 4.0.0 与两份现役镜像匹配 |
| LS authority（在线精确树） | 通过 | 完整 vendor 与精确上游 SHA 一致；不是所有消费者生产已升级 |
| Skill release --check | validated，33/33，466 files，9,220,727 wire bytes | 完整包本地校验；未 publish，未查线上 pointer |
| 8 个聚焦 Node 测试文件 | 83 tests：73 pass、1 fail、9 skip | 不是全量 IDE suite；skip 是缺本机 AGENTS.md 的明确条件 |
| 3 个聚焦 Vitest 文件 | 53 pass：目录 17、进度 30、运行时 6 | 初始与中间 SHA 均通过；最终仅核对不改变实现/输入的文档删除，不是全量 Vitest |
| Wiki pytest / MCP | 43 pass / 0 结构错误 | 与 IDE 测试分开；提交时间相关 stale 提示在提交后复核 |
| check-guidance | 失败 1 项 | HANDOFF.md:76 引用主线不跟踪的 AGENTS.md |
| IDE Control capability inventory | 失败 2 个缺项 | refreshStepProgress 私有命令、importStylePack webview 消息未登记 |

测试版本：Node、Vitest 与 guidance 实际运行于 `14c089322` 和 `49adbc3fb`；`442fd5369` 仅做完整删除差异核对，未伪称另跑同一套。在线 LS 与 Skill 完整包检查的初始版本输入在最终主线不变。

聚焦测试文件：single-agent-runtime、check-repository-migration、lunascripts-authority、ide-skill-release、agent-guidance-contract、product-information-contract、stage-pi-runtime、ide-control-capability-inventory（均在 test/，后缀 .test.mjs）。唯一失败来自最后一项的仓库完整性测试，与两条 inventory 缺项一致。

## 源仓待修问题（没有在本次擅自修改 IDE）

1. `HANDOFF.md` 链接不存在的 tracked AGENTS.md，而相关测试承认 AGENTS 是本机 ignored 指导。应统一文档链接与分发策略，不能把 9 项 skip 当成通过，也不应自动公开个人 AGENTS 文件。
2. `config/ide-control-capability-inventory.json` 漏登记 `lunaverse.workshop.refreshStepProgress` 和 `workshop.importStylePack`。应由 IDE 维护者明确 disposition，再生成 inventory；不能仅删除断言来变绿。
3. `agents/README.md` 仍泛化“直接 atomic CLI”和默认隐藏后台执行，与当前系统提示要求普通媒体先过 `lunaverse_produce`、后台需用户明确要求冲突。Wiki 依据当前宿主接线校准，并保留该源文档漂移为问题。
4. 中文使用手册及 Laminar runbook 含较早架构/发布/候选状态。不能因为文件名叫 runbook 就覆盖当前客户端协议；后续应按同一固定证据更新源仓文档。
5. Skill Publisher 当前硬编码生产数量 33。新增/删除 Skill 需要同步发布合同与测试；仅放一个目录不足以完成上线。这是当前显式完整性约束，不是任意规模的通用规则。

这些问题不妨碍本次把 Wiki 写对，但阻止声称“IDE 主线所有检查通过”。

## Wiki 完整性验收

原有 87 页逐项处置，32 页字节级未改，23 页历史正文和 24 页跨项目正文完整保留；55 个既有页面更新、5 个新增，形成 92 页。初始版本的 120 个不同固定源码路径全部有效；现行页补记最终 SHA 与增量来源。中途加入又撤回的开发指南不作为当前执行规范；保留历史链接可追溯撤回过程。真实 MCP 查询可检索新知识，已有 raw 无改动。新增文本有限凭据模式扫描零命中，不将其称为完整安全审计。

## 未验证边界

没有运行安装版 UI/可访问性/视觉体验复核、完整打包签名公证、自动升级、真实登录/注册/配额、云端 Skill 33 包回读、付费图像/视频/音乐/音效、Voice/TTS、项目快照生产恢复、作品发布到玩家可见、Laminar 线上 trace。也未审计独立 IDE Cloud、App Backend、Router 或 Client 当前主线内部实现。

保留的旧故障、benchmark、Dream/推荐/经济与其他产品文档标注了范围；不相关页只做目录/关联排查，不能因为被列入 87 页账本就称其所有业务事实已验证。本次也没有承诺公开 Wiki 与私有 IDE 源码的权限相同：固定 GitHub 来源链接需要团队自己的仓库访问权。

## 如何持续整理进 Wiki

一次有效入库应保留：权威 repo + exact SHA、事实对应文件、观察方式、测试结果/跳过/失败、线上与本地的差别。原始摘要进入 dated raw 后通过 wiki_ingest 获取上下文，再更新当前 entity/concept、历史标记、目录和日志；不是自动把 README 或聊天记录原封不动当现状。

提交前检查相互引用及 current/historical 边界；精确链接固定证据，若使用滚动 main 应明确其可能变化。原 raw 不覆盖，新的事实用新的 raw 追加。没有运行的步骤写清没运行，不用源码存在、CI 绿灯或用户口头“应该通”替代验收。

### 当前阅读入口

[[entities/lunaverse-ide]] → [[concepts/lunaverse-ide-ai-integration]] / [[concepts/lunaverse-ide-skills-and-production]] / [[concepts/assets-produce-ide-workspace-contract]] / [[concepts/lunaverse-ide-creator-progress]] / [[concepts/lunaverse-ide-ls-contract]] / [[concepts/lunaverse-ide-release-and-operations]]。

完整逐页处置与源码目录清单见 [校准附录](https://github.com/cdotlock/mob-wiki/blob/main/docs/ide-calibration-2026-09-15.md)。附录在仓库 docs 中，不属于 MCP 全文索引；通过 GitHub 阅读。Wiki 自身最终测试、结构检查与发布提交以同批日志和 Git 提交为证，不拿 IDE 聚焦测试替代。

## 核对来源

- [scripts/check-repository-migration.mjs](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/scripts/check-repository-migration.mjs)
- [scripts/check-guidance.mjs](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/scripts/check-guidance.mjs)
- [scripts/check-lunascripts-authority.mjs](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/scripts/check-lunascripts-authority.mjs)
- [scripts/ide-skill-release.mjs](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/scripts/ide-skill-release.mjs)
- [scripts/check-ide-control-capability-inventory.mjs](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/scripts/check-ide-control-capability-inventory.mjs)
- [config/ide-control-capability-inventory.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/config/ide-control-capability-inventory.json)
- [test/agent-guidance-contract.test.mjs](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/test/agent-guidance-contract.test.mjs)
- [HANDOFF.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/HANDOFF.md)
- [agents/README.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/README.md)
- [packages/agent-runtime/src/lunaverse-system-prompt.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/agent-runtime/src/lunaverse-system-prompt.ts)
- [packages/ls-preview/src/production-release-client.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-preview/src/production-release-client.ts)
- [本次原始核对记录](../../raw/2026-09-15-lunaverse-ide-main-calibration.md)
- [已撤回的历史开发指南：README.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/49adbc3fb31362195ed4b6e1a7111aaab3e89dff/docs/development/README.md)
- [已撤回的历史开发指南：git-and-pull-request.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/49adbc3fb31362195ed4b6e1a7111aaab3e89dff/docs/development/git-and-pull-request.md)
- [已撤回的历史开发指南：worktree-build-test.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/49adbc3fb31362195ed4b6e1a7111aaab3e89dff/docs/development/worktree-build-test.md)
- [补充验证与最终主线差异](../../raw/2026-09-15-lunaverse-ide-calibration-verification.md)
- [最终主线与开发指南撤回](../../raw/2026-09-15-lunaverse-ide-calibration-final-main.md)
