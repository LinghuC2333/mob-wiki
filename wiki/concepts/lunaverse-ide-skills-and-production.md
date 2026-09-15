---
title: Lunaverse IDE — 技能分发与生产执行
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

IDE 的领域能力以 Git 中的完整 Skill 目录维护，经 R2 与认证网关发布，再投影到作品中供 Pi 渐进读取。技能告诉模型怎样做；真正的付费生产准入、任务持久化与成果写回由 IDE 宿主负责，两者不能互相替代。

本页校准截至 main `442fd53692d077c1e601231d79792c6c50b430e3`；实现引用保留最初核对的 `14c089322ba14c06b65fcb17d9ecdfd4d807b8d3`。期间两次开发资料变更已核对，最终净差异仅删除三份本机开发 Skill，产品实现未变。版本与测试归属见 [[syntheses/lunaverse-ide-calibration-2026-09]]；这不是实时订阅。

## 权威与完整包

本次固定提交有 **33 个生产 Skill、466 个完整包文件、9,220,727 wire bytes**，本地发布检查为 `state=validated`。这表示源码包通过检查，不表示 production 指针已更新或成员设备已同步。

`agents/` 的 adaptation、asset、audio、minigame、script、manga-cg 是领域命名空间；`_shared` 是共享材料，script 目录本次没有生产 Skill。33 个 wire name 的完整名单、各自源码目录与文件数见校准附录。

每个 `SKILL.md` 的 frontmatter 使用 `name` 与 `description`，目录名与原始 name 一致。`allowed-tools` 不应被当成权限控制。一个完整包包含正文、references、scripts、assets、fixtures 中实际需要的文件，不能只传正文而丢 companion。

## 发布路径

1. 修改 `agents/**/skills/<name>/` 权威源，运行相关测试及 `pnpm skills:release:check`。
2. 按 IDE 仓协作规则提交并进入 main；Wiki 的直接 main 授权不自动扩展到 IDE、Cloud 或其他仓库。
3. `.github/workflows/skill-r2-production-publish.yml` 对匹配路径的 main push 或手动 dispatch 发布精确提交；工作流限定权威仓与 main。
4. Publisher 检查生产源集 33/33、安全路径、大小、权限与 hash，写不可变 bundle 和 catalog，逐项读回。
5. 以条件写最后切换 production pointer，并核对指针、catalog、release ID 与数量后才报告 published。移除用 tombstone 显式表达。

团队成员不处理 R2 密钥；凭据属于既定 CI 环境。不要手动改不可变对象或在控制台随意改 production 指针。当前发布器没有通用指针回滚命令，回滚必须遵循对应服务 runbook，不能把旧 catalog 路径猜成可直接写入的目标。

## 运行时投影

Workbench 将可用技能投影到当前作品 `.lunaverse/skills/`。模型先看目录元数据，任务匹配后读取正文，再按正文要求读参考。投影有命名和路径适配，不能把投影后字节直接等同于 Git 源文件字节。

运行时先验证本地 receipt 和完整文件树，再带 ETag 请求认证 catalog。304 复用有效本地包；有变化时取 `bundle-v1`，校验根 hash、每文件 hash、大小、路径和正文后原子替换并记录来源。网关错误不能删除 receipt-valid 的 last-known-good；只有完整认证 catalog 的 tombstone 可以退役技能。

旧 body-only 响应保留兼容，但精简安装包缺 companion 时不能用正文凑出可用 Skill。新设备没有有效本地包又拉取失败时，应明确显示不可用，不得承诺总能离线回退。

Langfuse **不属于 Skill 发布或运行时交付链**。风格机制须单独看 [[concepts/style-langfuse-migration]]，不能因名称相似混在一起。

## 写作与媒体生产的分工

| 任务 | 当前执行方式 | 完成依据 |
|---|---|---|
| 小说评估、Bible、规划、实体修订、分集写作与审查 | 主会话读取现役 Skill 并执行；仅按用户或适用 Skill 要求委派 | 规范成果、实际 reviewer 结论和机械检查 |
| 普通 Gallery 图像、音频、视频 | 从 `lunaverse_produce` 进入宿主生产准入 | 持久化 Run/Job 回执、规范成果与终态 |
| Manga / Minigame 专家例外 | 仅当宿主明确返回 `PRODUCTION_REQUIRES_MAIN_AGENT` 且 `providerStarted=false`，按指定 Skill 与 run-local tools 继续 | 专家流程真实产物和宿主验证 |
| 发布 | `lunaverse_publish`，跟踪到终态 | 版本绑定、workflow/runtime truth；不是 release scan 绿灯 |

主 Agent 不得绕过准入直接调用供应商、手写 HTTP 或重复提交视频请求。`assetctl`、`videoctl`、Manga remote 是拥有边界的原子工具，不是绕过付费或权限的后门。发现 `agents/README.md` 的旧“直接 CLI/默认后台执行”泛化说明与当前系统提示冲突时，以当前宿主接线和运行规则为准；此冲突已列源仓待修项。

## 当前媒体契约

- 视觉采用系列角色立绘 → outfit anchor → episode sprite；普通角色透明图现在走 provider 原生 2K alpha 与本地确定性收尾，不应复用绿幕时代的抠图/放大部署说明。
- CG 可选择静态图、视频或动态漫画；视频生产由 `videoctl` 的可恢复任务链承担，不能由另一个 atom 重复提交同一请求。
- Audio Production 产 BGM/SFX 到 `08-audio-production/{music,sfx}`；Voice Casting 保存 `voices.json` 并试听，角色对白 TTS 与发布链另行管理。
- Minigame 以完整游戏目录交付 `09-minigame-production/games/<gameId>/`，按任务需要逐步深化 Layer 1/2/3。
- 已有成果默认保留；用户明确要求修订或重生成时可以改，不能把“已渲染”当作不可变限制。

## 回执与创意复核

生产预览工具可以显示目标、输入缺口、引用与潜在付费操作，但不能捏造报价或把预览当提交。只有成功生产回执才说明 Run 已创建；启动进程、超时或仍运行都不是成功。

创意/语义审核若被用户明确接受，须由 `lunaverse_review_override` 绑定当前成果 hash、原生确认与理由后再继续；不能编辑 checkpoint 假造通过。此操作不能豁免损坏文件、alpha、mapping、安全、计费或发布要求。

相关：[[concepts/assetctl-integration-contract]] · [[concepts/agent-manuals-agents-md]] · [[concepts/lunaverse-ide-creator-progress]] · [[concepts/lunaverse-ide-release-and-operations]]。

## 核对来源

- [scripts/ide-skill-release.mjs](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/scripts/ide-skill-release.mjs)
- [.github/workflows/skill-r2-production-publish.yml](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/.github/workflows/skill-r2-production-publish.yml)
- [docs/runbooks/skill-gateway-parity.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/docs/runbooks/skill-gateway-parity.md)
- [packages/ls-workbench/src/cline-skill-projection.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-workbench/src/cline-skill-projection.ts)
- [packages/ls-workbench/src/cline-skill-gateway.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-workbench/src/cline-skill-gateway.ts)
- [packages/agent-runtime/src/lunaverse-system-prompt.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/agent-runtime/src/lunaverse-system-prompt.ts)
- [packages/ls-agent/src/production-mcp.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-agent/src/production-mcp.ts)
- [agents/asset/cli/bindings.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/asset/cli/bindings.json)
- [vendor/assetctl/internal/tools/registry.go](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/vendor/assetctl/internal/tools/registry.go)
- [本次原始核对记录](../../raw/2026-09-15-lunaverse-ide-main-calibration.md)
- [补充验证与最终主线差异](../../raw/2026-09-15-lunaverse-ide-calibration-verification.md)
- [最终主线与开发指南撤回](../../raw/2026-09-15-lunaverse-ide-calibration-final-main.md)
