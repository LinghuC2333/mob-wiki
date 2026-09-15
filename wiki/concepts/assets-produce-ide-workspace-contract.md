---
title: Lunaverse IDE — 规范书籍目录与本地素材映射
tags:
- assets-produce
- lunaverse-ide
- mapping-json
- asset-pipeline
- contract
sources:
- raw/2026-09-15-lunaverse-ide-main-calibration.md
- raw/2026-09-15-lunaverse-ide-calibration-final-main.md
- raw/2026-09-15-lunaverse-ide-calibration-verification.md
created: '2026-05-18'
updated: '2026-09-15'
last_reviewed: '2026-09-15'
status: current-source-snapshot
source_revision: 442fd53692d077c1e601231d79792c6c50b430e3
evidence_revision: 14c089322ba14c06b65fcb17d9ecdfd4d807b8d3
---

当前 IDE 用一个规范目录承载一本书，先在本地形成可检查的成果，再由发布流程生成云端版本。本页替代 2026-05 的“根目录 mapping.json 是唯一事实、IDE 不扫描磁盘”约定；那些旧约定不能用于新工程。

本页校准截至 main `442fd53692d077c1e601231d79792c6c50b430e3`；实现引用保留最初核对的 `14c089322ba14c06b65fcb17d9ecdfd4d807b8d3`。期间两次开发资料变更已核对，最终净差异仅删除三份本机开发 Skill，产品实现未变。版本与测试归属见 [[syntheses/lunaverse-ide-calibration-2026-09]]；这不是实时订阅。

## 书籍与工作区

Library 工作区的新书目录是 `books/<bookId>/`。`book.json` 提供作品元数据，宿主识别并绑定当前书；既有注册路径和 `lunascripts/<bookId>/` 有兼容读取逻辑，不应机械移动用户已有工程或把本地旧目录名误认为产品未迁移。

默认资料库位于用户目录下 `Lunaverse IDE/workspace`。变更资料库位置应使用 Lunaverse: Change Workspace Location 的安全迁移操作，不能只改设置路径然后假设旧书已搬过去。

## 新书规范目录

| 路径（相对 bookRoot） | 放什么 |
|---|---|
| `book.json` | 作品身份、标题和创建元信息 |
| `00-source/chapters`、`00-source/cover` | 原文与书级封面；原文/创作前提属于创作输入 |
| `01-novel-evaluator/` | 评估、改编 brief、独立复核报告 |
| `02-character-architect/bibles/` | 角色 Bible 与上层 bible-review-report |
| `02.5-outfit-anchor/` | 着装锚点规划材料 |
| `03-entity-planner/routes/` | 结构决策、分集与路由规划 |
| `03.5-vault/{routes,episodes,characters,locations,wardrobe,secrets}/` | 本书结构化创作知识；secrets 指剧情秘密，不是系统凭据 |
| `04-entity-normalizer/` | characters、locations、alias_map 规范实体 |
| `04.5-entity-rename/` | rename_map 和对应变更报告 |
| `05-episode-writer/{scripts,drafts,reviews}/` | 正式 LS、草稿与逐集审核；正式文件按 Skill 的 episode 身份命名 |
| `06-asset-prompt-generator/asset-specs.json` | 素材描述/计划输入，不等于已渲染成果 |
| `07-asset-production/images/{character,anchor,ep_sprites,scene,cg}/` | 系列角色、锚点、逐集立绘、场景和 CG 图像 |
| `07-asset-production/mapping.json` | 当前本地素材映射 |
| `08-audio-production/{music,sfx,auditions}/` | BGM、音效、试听缓存 |
| `08-audio-production/voices.json` | 角色声音分配，不是整集对白音频 |
| `08-cg-production/runs/` | CG 生产运行资料 |
| `09-minigame-production/games/<gameId>/` | 完整 H5 小游戏，入口通常 index.html |
| `10-preview/compiled/` | 本地预览派生输出 |
| `11-publish/bundles/` | 发布派生包 |

## 不同状态目录不要混用

`book-layout.ts` 另定义工作区 `.lunaverse/state/<bookId>/`，其 cache、runs、asset-history、releases 供宿主使用。其中 `codex-home` 等 legacy 命名的路径函数存在，不证明当前仍执行 Codex。

书内 `.lunaverse/skills/` 是受管理技能投影，`.lunaverse/creator-step-progress.json` 是步骤记录，`.lunaverse/production/active-style.json` 是本书选定风格身份。它们分别由各自拥有者更新，不应把所有 `.lunaverse` 一次删除当作通用恢复操作。

## 映射与磁盘存在性

Gallery 会读取本书磁盘状态；`local-mapping.ts` 的 `discoverLocalAssetInventory`、构建与补全逻辑可以从当前素材和磁盘清单派生本地 mapping。读取映射时先尝试 `07-asset-production/mapping.json`，再考虑旧根 mapping。

映射仍是 Preview/compiler 解析素材引用的重要输入，但不是“有一张 mapping 就证明文件存在”。本地 loc 必须能解析到当前书的真实非空交付文件；路径、kind、角色/look、CG 形态要匹配。远端 URL 存在于 JSON 也不等于本次网络探测成功。

不要沿用 `patch_mapping.py --apply` 的旧说明，在当前书中造出不存在的图片条目。规范生产成功后由宿主更新 Gallery/mapping；孤立文件需要识别、验证后登记，不能只靠文案宣布“已同步”。

## 预览、备份与恢复

本地 Preview 允许消费本地素材，并不意味着所有发布条件已满足。云端 player 不能直接访问这台机器的路径，因此发布必须走拥有上传和版本绑定的正式链。

完整工程快照的意义不同于 assets.zip：它绑定工程清单、文件 digest 和 blob；恢复会校验路径、大小与 SHA-256，并采用隔离暂存。恢复到新目标，保留原工程；不能把任意 ZIP 改名为 project.zip 后当作可信快照，也不能用恢复动作绕过当前权限。

相关：[[concepts/lunaverse-ide-creator-progress]] · [[concepts/lunaverse-ide-release-and-operations]] · [[concepts/lunaverse-ide-ls-contract]]。

## 核对来源

- [packages/shared/src/node/book-layout.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/shared/src/node/book-layout.ts)
- [packages/ls-welcome/package.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-welcome/package.json)
- [packages/ls-workshop/src/local-mapping.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-workshop/src/local-mapping.ts)
- [packages/ls-workshop/src/active-style-authority.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-workshop/src/active-style-authority.ts)
- [packages/ls-preview/src/project-snapshot.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-preview/src/project-snapshot.ts)
- [packages/ls-preview/src/project-snapshot-client.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-preview/src/project-snapshot-client.ts)
- [packages/ls-preview/src/project-snapshot-restore.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-preview/src/project-snapshot-restore.ts)
- [本次原始核对记录](../../raw/2026-09-15-lunaverse-ide-main-calibration.md)
- [补充验证与最终主线差异](../../raw/2026-09-15-lunaverse-ide-calibration-verification.md)
- [最终主线与开发指南撤回](../../raw/2026-09-15-lunaverse-ide-calibration-final-main.md)
