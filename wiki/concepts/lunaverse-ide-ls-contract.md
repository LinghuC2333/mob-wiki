---
title: Lunaverse IDE — LS 4.0 消费契约与历史兼容
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

当前 IDE 是 Lunascripts 语言的消费者，不是另一套语法规范的拥有者。本次固定主线消费上游 `cdotlock/lunascripts@515a9e3bf677e033d069a56038066ddf9b47adf5` 的 **4.0.0** 契约；已在线取回该精确提交，验证完整 vendor 树一致，并检查两份现役 Skill 镜像逐字节一致。

本页校准截至 main `442fd53692d077c1e601231d79792c6c50b430e3`；实现引用保留最初核对的 `14c089322ba14c06b65fcb17d9ecdfd4d807b8d3`。期间两次开发资料变更已核对，最终净差异仅删除三份本机开发 Skill，产品实现未变。版本与测试归属见 [[syntheses/lunaverse-ide-calibration-2026-09]]；这不是实时订阅。

## 权威链

| 材料 | 作用 |
|---|---|
| 上游 `LS-SPEC.md` | 作者语法与舞台语义 |
| `contract/contract.json` | 契约版本、signal 命名、兼容与 rollout 声明 |
| `contract/episode.schema.json` | 编译后 episode JSON 结构 |
| `docs/JSON-OUTPUT.md` | 编译输出语义 |
| IDE `vendor/lunascripts/` | 精确上游快照，升级需整体同步 |
| `agents/_shared/knowledge/LS-SPEC.md` | 共享创作指导镜像 |
| `agents/adaptation/skills/episode-writer/ls-spec.md` | Episode Writer 的镜像 |

本次验证的是被 IDE 消费的固定上游 revision，不是宣称上游远端此后没有新提交。`vendor/lunascripts/build-info.json` 中 `development` 字样不能替代 vendor README 的精确 pin。

## 作者最容易沿用错的规则

- 新正式剧本使用 `.ls`，一文件一集；`.ls.md` 和旧输入的兼容不应成为新写作默认。
- 内心独白用 `INNER_THOUGHT:`，编译输出节点 `inner_thought`。旧 `YOU:` 仍可被编译器接受；反编译两种节点均输出新写法。
- 角色每集首次对白前必须声明 look；MC 第一次 INNER_THOUGHT 前也要声明。内心独白显示 MC 的最近 look，不能沿用旧 Wiki 的“YOU 与 NARRATOR 都清屏”推论。
- 作者 mark/int signal 用 `SCREAMING_SNAKE_CASE`，模式 `^[A-Z][A-Z0-9_]*$`；引擎小写只读数值命名空间保持独立。
- `@<char> <look>` 是当前呈现语法；旧 `show/look/move/hide` 动词不能按旧流水线说明任意发明。
- 素材语义键、Look 生产命名与解析器透传是不同层；不可只改例子字符串而不验证编译/映射。

## 指令类别覆盖

当前规范包括结构 `@episode/@gate/@pause`；视觉 `@<char> <look>`、`@<char> bubble`、`@bg/@cg`；对白 `CHARACTER:`、`CHARACTER [look]:`、`NARRATOR:`、`INNER_THOUGHT:`；手机 `@phone/@text`；音频 `@music/@sfx`；交互 `@trick/@minigame/@choice/@option/check`；状态 `@affection/@signal mark/@signal int/@achievement/@butterfly`；流程 `@if/@else @if/@else`。`@` 开新步骤，`&` 参与前一步并发组。

这份分类用于避免漏审，不替代语法参数和 JSON schema。完整逐参数参考以同一固定版本的权威文档为准，不能从旧 Wiki 拼出混合语法；语法示例进入正式项目之前用该版本 `lsc` 校验。

## 消费兼容而不是存量改写

v4 rollout 明确要求消费者在启用 v4 编译器前兼容 v3/v4 episode 与 `you/inner_thought` 两类节点。既有 step ID tags、MC staging identifiers 保持不变，存量内容策略是 `audit_only`。

因此不要把“Wiki 更新到 v4”解释为允许批量改写已发布 LS、JSON、玩家存档、DB 列或上线指针。Backend、IDE Cloud 和播放器的运行时接收能力需要各自提交/部署/验收证据，本次未验证这些生产环境。

## 编译与回归边界

`node scripts/check-lunascripts-authority.mjs` 检查声明版本与两份镜像；加 `--online` 才核对完整 vendor 与精确上游树。它们通过不等于 GUI 高亮、LSP、F Studio、Preview、后端和真实播放器所有行为都做过端到端。

编辑器、素材扫描、编译输出、发布和运行时是不同消费面，语言升级必须逐面跟踪。本次校准没有升级编译器或改应用代码，只纠正 Wiki 的权威与兼容说明。

历史材料 [[concepts/ls-format]]、[[concepts/ls-spec-redesign-2026-06]] 保留六月快照；[[concepts/signal-int-backend]]、[[concepts/stable-step-id]] 中的后端实现不能仅据 IDE 仓推断今天仍完全一致。

## 核对来源

- [vendor/README.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/vendor/README.md)
- [vendor/lunascripts/contract/contract.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/vendor/lunascripts/contract/contract.json)
- [vendor/lunascripts/contract/episode.schema.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/vendor/lunascripts/contract/episode.schema.json)
- [vendor/lunascripts/LS-SPEC.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/vendor/lunascripts/LS-SPEC.md)
- [vendor/lunascripts/docs/JSON-OUTPUT.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/vendor/lunascripts/docs/JSON-OUTPUT.md)
- [scripts/check-lunascripts-authority.mjs](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/scripts/check-lunascripts-authority.mjs)
- [本次原始核对记录](../../raw/2026-09-15-lunaverse-ide-main-calibration.md)
- [补充验证与最终主线差异](../../raw/2026-09-15-lunaverse-ide-calibration-verification.md)
- [最终主线与开发指南撤回](../../raw/2026-09-15-lunaverse-ide-calibration-final-main.md)
