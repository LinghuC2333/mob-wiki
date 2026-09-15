---
title: Lunaverse IDE — 当前风格包与历史 Langfuse 迁移
updated: '2026-09-15'
created: '2026-06-02'
tags:
- style
- langfuse
- migration
sources:
- raw/2026-09-15-lunaverse-ide-main-calibration.md
- raw/2026-09-15-lunaverse-ide-calibration-final-main.md
- raw/2026-09-15-lunaverse-ide-calibration-verification.md
last_reviewed: '2026-09-15'
status: current-source-snapshot
source_revision: 442fd53692d077c1e601231d79792c6c50b430e3
evidence_revision: 14c089322ba14c06b65fcb17d9ecdfd4d807b8d3
---

“Skill 不再经 Langfuse”与“所有风格支持都已删除”是两件事。当前源码仍有网关/Langfuse 兼容和版本管理代码，但新风格选择由完整随包 recipe 的本地目录控制，不再适用 2026-06 的“16 个远端 family 是唯一现行目录”描述。

本页校准截至 main `442fd53692d077c1e601231d79792c6c50b430e3`；实现引用保留最初核对的 `14c089322ba14c06b65fcb17d9ecdfd4d807b8d3`。期间两次开发资料变更已核对，最终净差异仅删除三份本机开发 Skill，产品实现未变。版本与测试归属见 [[syntheses/lunaverse-ide-calibration-2026-09]]；这不是实时订阅。

## 当前选择权威

`bundled-style-packs.ts` 明确列出三个随包 ID：`impasto-huan`、`flat-falling`、`impasto-arcane`，对应 family `impasto_huan`、`flat_falling`、`impasto_arcane`。包中有 style manifest、prompt、reference 资源及共享 workflow/production policy；完整 recipe 不是一段可随意替换的 prompt 文本。

加载时按文件内容形成 revision hash；可复制到按 revision 命名的用户存储，以免应用更新使既有引用失效。若安装没有完整 built-in packs，代码明确报错，不以“远端有一行目录”假装包完整。

`localFirstStyleCatalog` 当前只合入完整 local pack，并不调用其 legacy remote callback；`mergeLocalStyleCatalogRows` 过滤新选择集合，避免旧/custom/cloud 行在刷新时重新成为新生产选项。旧数据保留在磁盘与冻结运行中，不能因此直接删除。

## 书籍风格与运行模板

本书选择写入 `.lunaverse/production/active-style.json`，字段为 `version=1`、`activeProfile`、`catalogRevision`、`activatedAt`。写入采用相邻锁和原子 rename，避免并发产生混合身份。

记录的 catalogRevision 是来源证据，不是永远冻结生产的锁。新生产仍需解析现行可用目录；精确 prompt/reference 在各 production execution template 固定。退役 profile 对新生产会报明确问题；允许历史预览的路径不能自动授权继续用退役模板出新素材。

## 仍存在的兼容面

`gateway-style-catalog.ts`、`gateway-style-write.ts`、`gateway-style-versions.ts` 和 Langfuse store 仍在仓库，宿主的部分 headless/管理路径仍消费认证网关目录。它们存在不表示每个运行时入口都采用同一数据源，也不能用一个旧 Langfuse URL 代替当前 recipe。

扩展 manifest 仍贡献 `lunaverse.styles.langfuse.*` 设置；这些是兼容/管理面，不是普通成员必须配置的生产秘密。不要复制其他仓库 `.env` 的“真 key”，不要将管理员凭据写到书籍或 Wiki；普通操作使用当前登录与宿主提供的入口。

## 2026-06 记录如何使用

六月的 16-family seeding、旧 style_* 命名、Cloudflare 1010、OSS→R2 reference 镜像、Python 三层 fallback 只说明当次迁移和故障背景，不能证明今天目录数量、线上配置或已安装 IDE 的权限。历史全文见 [校准前版本](https://github.com/cdotlock/mob-wiki/blob/20b102e7a516d6f15cb66383a8c98eed4be4d68a/wiki/concepts/style-langfuse-migration.md)。

维护时先确认受影响的是新风格选择、历史预览、管理写入还是某个 production template，再追踪对应源文件。若本地与远端目录不同，不直接把其中一份覆盖到所有入口。

相关：[[concepts/lunaverse-ide-skills-and-production]] · [[concepts/assets-produce-ide-workspace-contract]] · [[syntheses/lunaverse-ide-calibration-2026-09]]。

## 核对来源

- [packages/ls-workshop/src/bundled-style-packs.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-workshop/src/bundled-style-packs.ts)
- [packages/ls-workshop/src/local-first-style-catalog.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-workshop/src/local-first-style-catalog.ts)
- [packages/ls-workshop/src/active-style-authority.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-workshop/src/active-style-authority.ts)
- [packages/ls-workshop/src/gateway-style-catalog.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-workshop/src/gateway-style-catalog.ts)
- [packages/ls-workshop/src/platform-adapter.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-workshop/src/platform-adapter.ts)
- [packages/ls-workshop/package.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-workshop/package.json)
- [本次原始核对记录](../../raw/2026-09-15-lunaverse-ide-main-calibration.md)
- [补充验证与最终主线差异](../../raw/2026-09-15-lunaverse-ide-calibration-verification.md)
- [最终主线与开发指南撤回](../../raw/2026-09-15-lunaverse-ide-calibration-final-main.md)
