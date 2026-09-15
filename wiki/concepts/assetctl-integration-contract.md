---
title: assetctl — 当前原子能力与宿主执行边界
created: '2026-05-20'
updated: '2026-09-15'
tags:
- assetctl
- assets-produce
- lunaverse-ide
- atomic-capability
- interface-contract
- codex
- oss-put
- generate-image-nanobanana
- generate-video-seedance
- generate-sfx-elevenlabs
status: current-source-snapshot
source_revision: 442fd53692d077c1e601231d79792c6c50b430e3
evidence_revision: 14c089322ba14c06b65fcb17d9ecdfd4d807b8d3
sources:
- raw/2026-09-15-lunaverse-ide-main-calibration.md
- raw/2026-09-15-lunaverse-ide-calibration-final-main.md
- raw/2026-09-15-lunaverse-ide-calibration-verification.md
last_reviewed: '2026-09-15'
---

`assetctl` 是 IDE 内的 Go 原子素材工具，不是主 Agent，也不是可以绕过登录和付费准入的通用执行入口。本次以 `vendor/assetctl/internal/tools/registry.go` 固定提交核对完整注册表，替代旧页累计 Wave 1–15 的数量与部署假设。

本页校准截至 main `442fd53692d077c1e601231d79792c6c50b430e3`；实现引用保留最初核对的 `14c089322ba14c06b65fcb17d9ecdfd4d807b8d3`。期间两次开发资料变更已核对，最终净差异仅删除三份本机开发 Skill，产品实现未变。版本与测试归属见 [[syntheses/lunaverse-ide-calibration-2026-09]]；这不是实时订阅。

## 三层“可用”要分清

源码注册表定义候选能力；`LUNAVERSE_ASSETCTL_DISABLED_TOOLS` 可屏蔽部分 ID；已安装二进制与登录网关又可能处在不同版本。因而本次 **23 个源码注册 ID** 不等于任何用户都能成功执行全部 23 项，也不证明每一项已跑真实供应商 E2E。

主会话的普通图像、音频、视频生产必须先经 `lunaverse_produce`，获得宿主绑定的输入、权限和回执；主 Agent 不应随意拼 CLI 直接发付费任务。只有宿主明确移交的专家流程使用相应 run-local tools。

## 发现与执行合同

由宿主提供的 `$ASSETCTL_BINARY` 指向正确打包工具。`tools list` 查当前 ID，`tools show <id>` 查完整输入 schema；`run <id> --input <JSON|@file>` 执行单项并返回 JSON envelope。不要使用 Wiki 的固定参数猜测覆盖 live schema，不要假设 PATH 中同名工具属于当前 IDE。

CLI 层负责参数校验、错误分类和解析；宿主/网关负责授权、配额、运行身份与付费重复提交保护。缺 IDE token 应回到 Lunaverse: Login；路由未部署应明确报错，不能转向本机私藏供应商 key。

## 当前注册表（完整）

| ID | 职责 |
|---|---|
| `generate-image-gpt` | 图像生成 |
| `generate-sfx-elevenlabs` | 音效生成 |
| `generate-music-suno` | 音乐生成 |
| `concat-clips` | 拼接片段 |
| `crop-video` | 视频裁切 |
| `generate-video-happyhorse` | 注册的 Happyhorse 原子；不能代替 Workshop 的 videoctl 提交链 |
| `cg-render` | 注册的 CG 原子；可调用范围仍由宿主与 schema 决定 |
| `r2-put` | 素材上传 |
| `hybrid-to-webp` | 本地格式收尾 |
| `audit-mapping` | 映射审计 |
| `parse-wardrobe` | 解析着装资料 |
| `check-clothing-keyword` | 服装关键词检查 |
| `check-clothing-llm` | 模型辅助服装检查；不能误标纯离线 |
| `process-cutout` | 当前本地透明图收尾；远程 matting 半链已移除 |
| `build-sprite-tasks` | 构造逐集立绘任务 |
| `build-character-prompts` | 构造角色图像描述 |
| `build-scene-prompts` | 构造场景描述 |
| `render-image-batch` | 图像批处理执行 |
| `build-audio-briefs` | 每个 music/SFX cue 的生成 brief |
| `build-cg-casts` | 解析 CG 画面角色与背景 |
| `review-character-portrait` | 角色立绘多模态复核 |
| `normalize-character-portrait` | 角色全身构图规范化 |
| `portrait-region` | 角色区域处理 |

这 23 项均在 `realTools()` 有实现绑定；这只是代码绑定检查，不是本次逐工具供应商验收。

## 已退役、不能沿用的路径

- 旧 18 / 25 项“永远 append-only”的表已不是当前全量表。
- `generate-image-nanobanana`、`nrbi-render-prompt` 已不在当前注册表。
- 绿幕时期 `cutout`、`green-spill-clear`、`rgb-unspill`、`hole-fill`、独立 `matting` 已移除。
- `upscale-image` 与 `process-cutout` 的远程阶段已移除；当前透明角色方案为原生 2K alpha + 本地收尾。
- `generate-video-seedance` 已迁出 assetctl，Workshop 视频由 `videoctl` 维护可恢复 submit/poll/download 身份。
- `extract-look-signatures`、`build-wardrobe-map`、`apply-look-aliases` 不再是注册 atom；保留的 parser 文件不代表工具仍对外注册。

因此不能照 [[concepts/comfyui-modal-deploy]] 或 [[concepts/asset-matting-hybrid]] 的历史部署命令来“修复当前 IDE 必需能力”。这些文章保留为历史事故/方案。

## 上下游

输入来自当前 book 的脚本、Bible、asset specs、宿主 manifest 与当前选中风格。输出由所属流程核验并映射到规范路径；一条 stdout 说明或远端 URL 不能单独证明本地落盘、Gallery 映射、Preview 和发布均完成。

当前 Skill 分发不使用 `assetctl skills sync/load` 的 Langfuse overlay。旧链见 [[concepts/assetctl-skills-sync-and-staging]]，新链见 [[concepts/lunaverse-ide-skills-and-production]]。

原页 Foundation/Wave 提交历史可从 [校准前版本](https://github.com/cdotlock/mob-wiki/blob/20b102e7a516d6f15cb66383a8c98eed4be4d68a/wiki/concepts/assetctl-integration-contract.md) 查阅。

## 核对来源

- [vendor/assetctl/internal/tools/registry.go](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/vendor/assetctl/internal/tools/registry.go)
- [agents/asset/cli/bindings.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/asset/cli/bindings.json)
- [packages/agent-runtime/src/lunaverse-system-prompt.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/agent-runtime/src/lunaverse-system-prompt.ts)
- [packages/ls-agent/src/production-mcp.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-agent/src/production-mcp.ts)
- [本次原始核对记录](../../raw/2026-09-15-lunaverse-ide-main-calibration.md)
- [补充验证与最终主线差异](../../raw/2026-09-15-lunaverse-ide-calibration-verification.md)
- [最终主线与开发指南撤回](../../raw/2026-09-15-lunaverse-ide-calibration-final-main.md)
