---
title: Lunaverse IDE 权威 main 整体 Wiki 校准原始记录
created: 2026-09-15
source_repository: https://github.com/MobAI-Inc/lunaverse-ide
source_revision: 14c089322ba14c06b65fcb17d9ecdfd4d807b8d3
wiki_baseline: 20b102e7a516d6f15cb66383a8c98eed4be4d68a
evidence_type: pinned-source-review-and-targeted-local-checks
---

# 本次范围

用户要求为 Wiki 对照当前 IDE 主仓做严格整体校准。2026-09-15 获取 MobAI-Inc/lunaverse-ide 远端 main，固定提交 14c089322ba14c06b65fcb17d9ecdfd4d807b8d3（提交时间 2026-09-15T12:20:51+08:00）。原 IDE 工作区有用户未提交文件，另建 detached 副本读取，未将这些编辑当成已合入主线事实。

87 个既有 Wiki 页经 MCP 读取、做目录与主题关联处置：6 页现行正文重写、23 页历史隔离、24 页加 IDE 关联边界、2 页目录/日志更新、32 页属于独立产品/后端范围而保留。新增 5 个现行专题，形成 92 页。保留原始历史，不修改已有 raw。

## 核心事实及直接证据

以下文件均相对同一个固定 IDE 提交。完整固定 GitHub 链接和逐页处置见同批 docs/ide-calibration-2026-09-15.md。

- scripts/check-repository-migration.mjs：桌面权威 MobAI-Inc/lunaverse-ide；Rydia-China 镜像、独立 IDE Cloud、release distribution 是有意的不同来源。
- services/ide-cloud/README.md：IDE Cloud 独立仓 cdotlock/lunaverse-ide-cloud 是源码与 Railway 发布权威，当前子目录为历史镜像。没有读取独立仓最新内部实现。
- fork/build.mjs + packages/*/package.json：VS Code 1.119.1，11 个顶层包，8 个内置扩展；manifest 与分发版本不能混用。
- packages/agent-runtime/src/runtime-selector.ts + packages/ls-agent/src/runtime-config.ts：Pi-only；统一登录与模型目录选择原生协议，不回退 Codex/shim。
- packages/ls-workbench/src/inline-completion.ts：Tab 仍调单轮 AgentAdapter/ChatBackend，并非纯本地补全。
- scripts/ide-skill-release.mjs + skill-r2-production-publish.yml + cline-skill-projection.ts：Git 完整 Skill → R2 不可变包/目录/指针 → 认证网关 → 校验投影；Langfuse 不是当前 Skill 分发链。
- packages/agent-runtime/src/lunaverse-system-prompt.ts + packages/ls-agent/src/production-mcp.ts：写作在主会话按 Skill；普通媒体先 lunaverse_produce；Manga/Minigame 仅按明确 providerStarted=false 的专家移交；发布走 lunaverse_publish。
- vendor/assetctl/internal/tools/registry.go：23 个源码 ID 和真实实现绑定；旧 matting/upscale/绿幕/Seedance 原子不能当当前产品链。注册不代表所有远端服务实测可用。
- packages/ls-workshop/src/bundled-style-packs.ts + local-first-style-catalog.ts + active-style-authority.ts：新选择受三个完整内置 recipe 限制；网关/Langfuse 兼容面仍存在，但旧 16-family 说明不是新选择权威。
- packages/shared/src/node/book-layout.ts + packages/ls-workshop/src/local-mapping.ts：books/<bookId> 规范目录，实际磁盘发现和 mapping 完整性；不是根 mapping 单一存在就完成。
- packages/shared/src/node/creator-step-progress.ts + creator-progress-tool.ts：Done/Processing/Not started，必需 reviewer 报告、局部范围与全书状态区分，不是自动质量或发布许可。
- packages/ls-preview/src/production-release-client.ts：IDE JWT 的 /api/ide 客户端协议，九种 canonical workflow state、readiness/fenced activation/runtime truth，兼容旧状态。
- project-snapshot.ts / project-snapshot-client.ts / project-snapshot-restore.ts：完整工程清单、digest、缺失 blob、finalize 与安全恢复，不等于 assets.zip。
- vendor/lunascripts/contract/contract.json：4.0.0；INNER_THOUGHT/inner_thought、作者 signal 大写、保留 step IDs，存量 audit_only。上游精确 SHA 515a9e3bf677e033d069a56038066ddf9b47adf5。

## 实际检查

隔离安装使用 Node 22、pnpm 10.26.2、frozen lockfile、offline、ignore-scripts；源码 tracked 文件未改。初次无 node_modules 的依赖缺失不是产品故障，装好隔离依赖后重新运行相关检查。

- repository migration guard：通过。
- LS authority 离线：通过；在线 fetch 精确上游并比较整个 vendor 树：通过。
- Skill release --check：state=validated；33/33；466 files；9,220,727 wire bytes。未 publish。
- 8 个 Node 聚焦文件：83 tests，73 pass、1 fail、9 skip。文件为 single-agent-runtime、check-repository-migration、lunascripts-authority、ide-skill-release、agent-guidance-contract、product-information-contract、stage-pi-runtime、ide-control-capability-inventory（test/*.test.mjs）。
- 唯一 fail：源仓 capability inventory 未列 lunaverse.workshop.refreshStepProgress 和 workshop.importStylePack；独立 scanner 同样报两缺项。
- 9 skip：源码明确跳过缺本机 ignored AGENTS.md 的指导测试，不计通过。
- check-guidance：HANDOFF.md:76 链接主线不跟踪的 AGENTS.md，1 issue。

## 发现而未修改的源仓问题

除两个检查缺口外，agents/README.md 中泛化直接 CLI/默认后台执行与当前 system prompt/宿主准入冲突；较早中文使用手册与 Laminar runbook 不是所有当前调用的权威。Wiki 按当前代码校准，源仓问题保留给后续明确实施范围。Publisher 33 的硬编码约束意味着加减 Skill 必须同步合同与测试。

## 未验证和未执行

未做全量 IDE check/Go/Vitest/GUI、安全审计、打包/签名/公证/热补/重启、真实登录注册、配额/限流压测、付费模型/素材、生产 Skill pointer 回读、发布到真实播放器、TTS、线上快照恢复、Laminar production trace。未修改数据库、权限、线上指针、工作流或独立仓库。

公开 Wiki 的来源是脱敏技术摘要与固定源码引用；私有源码链接需要团队仓库权限。此记录不包含 token、实际邀请码、生产 DB 内容、个人设置、trace 正文或未提交创作内容。完整覆盖账本不等于所有独立项目事实均已复验。
