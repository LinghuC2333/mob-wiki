---
title: Lunaverse IDE — 发布、工程恢复与维护验收
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

本页区分代码已合入、桌面安装、技能发布、作品上传和玩家可见五种不同结果。当前 IDE 使用统一登录访问 IDE Cloud 网关；旧“共享 admin cookie → App Backend submit → admin 手工 activate”文章只代表历史实现。

本页校准截至 main `442fd53692d077c1e601231d79792c6c50b430e3`；实现引用保留最初核对的 `14c089322ba14c06b65fcb17d9ecdfd4d807b8d3`。期间两次开发资料变更已核对，最终净差异仅删除三份本机开发 Skill，产品实现未变。版本与测试归属见 [[syntheses/lunaverse-ide-calibration-2026-09]]；这不是实时订阅。

## 服务拥有权

桌面与 Skill 源码归 `MobAI-Inc/lunaverse-ide`；IDE Cloud 源码/部署归 `cdotlock/lunaverse-ide-cloud`；App Backend 是独立消费者。桌面仓的 `services/ide-cloud/README.md` 明确声明该目录是历史镜像，旧 monorepo Railway release workflow 已退役。

模型、技能、媒体、账户和发布经过登录网关；默认域名是 `ide-api.playlunaverse.com`。这只是源码配置，不是本次 live health 证明。`/health` 即便 200 也只说明进程；`/ready` 证明的依赖范围亦不等同于完整创作、生成、TTS 或玩家端 E2E。

## 用户的发布入口

只要求检查时，用 Release Center 或 `lunaverse_release_scan`，后者仅接收 `{version: 1}`，不会发布。明确要求发布、重试或继续发布时，走 `lunaverse_publish`，输入 `{version: 1, changelog?: string}`，changelog 最多 4096 字符。

宿主识别当前书、扫描、显示原生确认、重查确认范围并上传，返回精确 release 身份。遇到结构化诊断，依据完整诊断修复有授权的源文件，重建受影响派生产物并重验；不能跳过 gate 或把 clear scan 当作发布成功。

## 客户端 wire（本次读到的合同）

统一 `Authorization: Bearer <IDE login>`，不需要团队成员提供旧 `noval_admin` cookie。请求由宿主绑定当前 novel，不从工作区搜索或猜 `latest/current` 身份。

| 操作 | HTTP 路径 | 请求字段 |
|---|---|---|
| claim 所有权 | POST `/api/ide/novels/:novelId/claim` | 可选 `title` |
| submit | POST `/api/ide/novels/:novelId/production/releases` | `manifest`，可选 `changelog/releaseLineKey/projectSnapshotId` |
| history | GET `/api/ide/novels/:novelId/production/releases/history` | 无 body |
| workflow | GET `/api/ide/content/releases/:releaseId/workflow` | 无 body |
| readiness | POST `/api/ide/content/releases/:releaseId/readiness` | `novelId/releaseId` |
| activate | POST `/api/ide/content/releases/:releaseId/activate` | `novelId/releaseId/expectedGeneration` |
| reconcile | POST `/api/ide/content/activations/:activationId/reconcile` | `novelId/releaseId` |

submit 回执要求有效 `releaseId`；还可包含 `status/manifestHash/idempotent/version/changesRequestedReason/counts/warnings/issues/runtimeReceipt`。网络未收回响应或成功响应不完整会被分类为 outcome unknown，不能盲目用新 key 重投。旧 releaseLineKey 协议兼容仅在明确的未知字段错误下处理，不能套成任意错误重试。

`lunaverse_release_workflow` 用 `{version:1, operation, novelId, releaseId, expectedGeneration?, activationId?}` 操作已知 release；operation 为 `status/readiness/activate/reconcile`，activate 使用当前 fence，reconcile 使用真实 activation ID。它不扫描当前本地文件，也不调用旧 promote 旁路。

## 状态不等于可见性

当前 canonical state 是 `uploaded`、`processing`、`ready`、`activating`、`app_prepared`、`runtime_active`、`superseded`、`failed`、`blocked`。旧 `pending/live/changes_requested` 仅兼容读取，不能再说“整个系统只有四种状态”或“成功 submit 永远 pending”。

workflow 包含 `novelId/releaseId/state/expectedGeneration/stateVersion/updatedAt`，以及 readiness、desired/effective release、activation ID、manifest/TTS hash 和失败详情。runtime truth 另报告 `baseVisible/effectiveReleaseId/appliedGeneration/capabilities/warnings/degraded`；这才用于解释玩家侧是否生效，而非凭上传百分比判断。

readiness 拆 `coreReady/checks/capabilities/warnings`，每个 check 有 code、severity（core/capability/warning）、ok、message，可选 capability。返回 novel/release 身份必须与请求一致，防跨书或旧版本混用。

## 完整工程快照

工程快照不是只有素材的 ZIP。客户端构造文件清单和 hash，创建 `/api/ide/novels/:novelId/project-snapshots`，上传缺失 blob，再 finalize `/api/ide/project-snapshots/:snapshotId/finalize`，绑定 projectSnapshotId 与发布。

恢复入口 `lunaverse.projectSnapshot.restore` 验证快照清单、路径、安全目标、大小和 SHA-256，在隔离目录构建后完成切换；不要覆盖唯一原始工程或把受管理会话/技能缓存当作随意重置对象。此处说明代码合同，本次没有创建生产快照或恢复用户数据。

## 外部 CLI

在应用内安装受管理 `lunaverse` CLI 后，用 `lunaverse doctor`、`ide list`、`catalog`、`tools show <id>` 发现实际实例和能力。完整命令与 flags 已从 `cli-args.ts` 的帮助文本收录到校准附录。

实例路由依次考虑明确 instance、workspace/book、当前目录最长匹配和唯一实例，不猜最近前台窗口。变更请求使用稳定 request ID；断线后查 requests status，后台工作查 jobs status/watch，取消也用幂等身份。jobs watch/events follow 可用 jsonl 和 sequence cursor，不能把取消受理当作供应商已终止。

退出码：0 成功、2 用法、3 IDE 离线、4 认证/策略、5 输入、6 领域失败、7 结果未知。普通 JSON stdout 是一个 envelope，诊断在 stderr。策略和 UI 确认不因通过 CLI 调用就被绕过。

## 开发检查与桌面分发

根 `pnpm check` 是 lint → typecheck → test → Go test；根 `pnpm test` 会先递归 build 再跑 Node suite，不能把它误认为只跑一个指定测试文件。服务包、Vitest、Go、安装版 E2E 另有入口；本次只运行列明的只读/本地聚焦检查。

桌面完整打包、签名、公证、上传、feed promotion、安装升级、Skill 发布、Router/Cloud/Modal 发布是不同流程。macOS 主发布入口为 Xcode Cloud，GitHub macOS release 仍是 fallback；Windows 有单独 release 和安装升级检查。仓库存在 workflow 或 candidate 不代表已成功上线。

本次文档校准没有请求完整打包，没有热补或重启安装应用，没有调付费 provider、改数据库、切指针或 dispatch 发版。可观测性复制诊断应保持脱敏；旧 Laminar runbook 标有 draft 候选，不能直接用其 Cline 时代说明证明当前线上采集。

验证结果、主线本身的问题与未运行的项目见 [[syntheses/lunaverse-ide-calibration-2026-09]]。旧后端实现历史见 [[concepts/production-pipeline-two-phase]]。

## 开发指导的撤回记录与权限边界

最终 main `442fd53692d077c1e601231d79792c6c50b430e3` 已删除上一观察版本的 `docs/development/README.md`、`git-and-pull-request.md`、`worktree-build-test.md`；三份 `.codex/skills` 本机开发 Skill 也没有恢复。不要把短暂存在的开发指南、其中的 Beta 热补要求或 Git 交付约定当成当前仓库已采纳的通用规范。下方 `49adbc3fb31362195ed4b6e1a7111aaab3e89dff` 的三个链接仅供历史追溯。

这一变化不删除产品 `agents/**/skills/**` 的 33 个领域 Skill，不改变运行时或发布实现。本机 AGENTS.md 与本次用户授权仍须在执行具体任务时分别读取；Wiki 直接 main 授权不自动扩大到 IDE 源码或部署。本次没有执行 IDE 热补、安装或发布。

## 核对来源

- [services/ide-cloud/README.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/services/ide-cloud/README.md)
- [.github/workflows/ide-cloud-railway-release.yml](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/.github/workflows/ide-cloud-railway-release.yml)
- [packages/ls-preview/src/production-release-client.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-preview/src/production-release-client.ts)
- [packages/ls-agent/src/production-mcp.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-agent/src/production-mcp.ts)
- [packages/ls-preview/src/project-snapshot-client.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-preview/src/project-snapshot-client.ts)
- [packages/ls-preview/src/project-snapshot-restore.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-preview/src/project-snapshot-restore.ts)
- [packages/ide-control/src/cli-args.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ide-control/src/cli-args.ts)
- [docs/runbooks/ide-cli.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/docs/runbooks/ide-cli.md)
- [package.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/package.json)
- [.github/workflows/xcode-cloud-release.yml](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/.github/workflows/xcode-cloud-release.yml)
- [.github/workflows/macos-release.yml](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/.github/workflows/macos-release.yml)
- [docs/runbooks/agent-observability-laminar.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/docs/runbooks/agent-observability-laminar.md)
- [本次原始核对记录](../../raw/2026-09-15-lunaverse-ide-main-calibration.md)
- [已撤回的历史开发指南：README.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/49adbc3fb31362195ed4b6e1a7111aaab3e89dff/docs/development/README.md)
- [已撤回的历史开发指南：git-and-pull-request.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/49adbc3fb31362195ed4b6e1a7111aaab3e89dff/docs/development/git-and-pull-request.md)
- [已撤回的历史开发指南：worktree-build-test.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/49adbc3fb31362195ed4b6e1a7111aaab3e89dff/docs/development/worktree-build-test.md)
- [补充验证与最终主线差异](../../raw/2026-09-15-lunaverse-ide-calibration-verification.md)
- [最终主线与开发指南撤回](../../raw/2026-09-15-lunaverse-ide-calibration-final-main.md)
