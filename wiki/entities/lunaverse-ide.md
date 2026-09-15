---
title: Lunaverse IDE
tags:
- ide
- vscode-fork
- ls
- cline
- codex
- assetctl
- voice-casting
- minigame
sources:
- raw/2026-09-15-lunaverse-ide-main-calibration.md
- raw/2026-09-15-lunaverse-ide-calibration-final-main.md
- raw/2026-09-15-lunaverse-ide-calibration-verification.md
created: '2026-05-30'
updated: '2026-09-15'
last_reviewed: '2026-09-15'
status: current-source-snapshot
source_revision: 442fd53692d077c1e601231d79792c6c50b430e3
evidence_revision: 14c089322ba14c06b65fcb17d9ecdfd4d807b8d3
---

Lunaverse IDE 是本地优先的互动故事创作桌面应用：管理作品、编写 LS 剧本、制作视觉与音频素材、预览和发布。本文描述 **2026-09-15 对照远端 main 固定提交的代码现状**，不是对已安装应用版本、线上服务或全部功能端到端可用性的承诺。

本页校准截至 main `442fd53692d077c1e601231d79792c6c50b430e3`；实现引用保留最初核对的 `14c089322ba14c06b65fcb17d9ecdfd4d807b8d3`。期间两次开发资料变更已核对，最终净差异仅删除三份本机开发 Skill，产品实现未变。版本与测试归属见 [[syntheses/lunaverse-ide-calibration-2026-09]]；这不是实时订阅。

## 权威仓库与版本

- 桌面主仓：`MobAI-Inc/lunaverse-ide`，本次提交 `14c089322ba14c06b65fcb17d9ecdfd4d807b8d3`。
- `cdotlock/lunaverse-ide` 是旧主仓；`Rydia-China/lunaverse-ide` 是有意保留的 Xcode Cloud 镜像，不应机械替换。
- IDE Cloud 的独立权威仓是 `cdotlock/lunaverse-ide-cloud`；桌面仓 `services/ide-cloud/` 明确为历史迁移镜像，不能由此部署新服务。
- VS Code 基线钉在 `1.119.1`；根 package 版本 `0.2.0` 不是分发版本。本次看到 `ide-2.0.3` release candidate，候选记录不等于当前设备或线上更新渠道版本。
- LS 消费契约为 `4.0.0`，来源与兼容边界见 [[concepts/lunaverse-ide-ls-contract]]。

## 团队成员怎样使用

先登录 Lunaverse，在 Library 创建或导入作品，再打开目标作品。也可以从 Agent 的导入入口建立独立草稿，再讨论如何导入；项目会话与作品绑定，关闭废弃导入入口不应被理解为删除原始文件。

| 入口 | 输入 | 用户得到什么 | 不能据此推断什么 |
|---|---|---|---|
| Library / 书库 | 原文、创作前提、现有工程 | 创建、导入、打开作品 | 导入成功不等于创作已完成 |
| Lunaverse Agent | 当前作品与明确任务 | 写作、修订、检查、生产及产品帮助 | 单一可见入口不等于只能开一个项目会话 |
| Creator Navigator / 作品导航 | 规范成果与步骤记录 | 原文、评估、Bible、Plan、Scripts、素材状态 | Done 不是自动质量认证 |
| Gallery / 画廊 | 当前作品的磁盘与生产快照 | 查看、检查和定位已识别素材 | 文件存在不等于所有映射/发布条件通过 |
| Voice Casting / 配音 | 角色与声音目录 | 选声音、试听、保存绑定 | 不是整集角色对白 TTS 生产 |
| F Studio / 经典编辑器 | 正式 `.ls` 剧本 | 可视化或文本编辑 | 不拥有另一套私有 LS 语法 |
| Preview / 预览 | 本地剧本与素材映射 | 播放当前本地构建 | 预览成功不是云端发布成功 |
| Release Center / 发布中心 | 当前书的版本、校验与发布任务 | 阻塞项、上传与运行时生效状态 | scan、upload、ready 都不等于玩家可见 |

提出“先检查第一集”不会授权整书重写或付费素材生成。提出实际生产请求后，由主 Agent 读取匹配技能并走宿主生产入口；不需要用户先手动挑选四个旧 Workshop Agent。

## 模块全景

`packages/` 有 11 个顶层包，其中 8 个在 `fork/build.mjs` 的内置扩展名单中；其余 3 个是共享库。不能把包数量、内置扩展数量、领域目录数量和模型进程数混为一谈。

| 包 | 类型 | 职责、输入与输出 |
|---|---|---|
| `ide-control` | 扩展 | 将外部 CLI 的窗口/书籍选择与有界 JSON 请求交给宿主能力，返回请求、Job、事件和安全诊断 |
| `ls-agent` | 扩展 | 会话 UI、登录与模型目录解析、Pi 宿主工具接线；输出可见回复及宿主验证的操作结果 |
| `ls-lang` | 扩展 | 注册 LS 语言、语法及 LSP；将源码交给固定版本语言工具 |
| `ls-studio` | 扩展 | 正式剧本的 F Studio 编辑与经典编辑器切换 |
| `ls-preview` | 扩展 | 编译预览、声音相关交互、发布数据生成、工程快照恢复 |
| `ls-workbench` | 扩展 | 工作区规则/技能投影、领域命令、单轮 Tab 补全及扩展协调 |
| `ls-welcome` | 扩展 | Library、登录、账户、作品导航、存储位置与语言切换 |
| `ls-workshop` | 扩展 | Gallery、素材扫描、生产准入与执行、风格、发布中心 |
| `shared` | 库 | 数据类型、路径、认证协议、LS/发布模型及跨端 Node 逻辑 |
| `agent-runtime` | 库 | Pi 会话、权限、检查点、文件 revision、子任务及运行事件 |
| `agent-adapter` | 库 | 保留单轮 ChatBackend 等适配能力；不能据此推断旧 CodexBackend 仍是产品运行时 |

其他根目录各司其职：`agents/` 是领域技能与配套资源；`vendor/` 放固定上游工具；`lsp-server/` 是 Go 语言服务；`fork/` 管桌面薄壳、补丁与分发；`scripts/`、`test/`、`tools/` 提供检查和验收；`services/ide-cloud-router/` 是路由组件。服务镜像的存在不改变独立仓库的权威。

## 当前架构的关键边界

1. **运行时**：Pi 是唯一产品 Agent 运行时。Cline/Codex 的历史目录、可观测性接入或兼容字段不能当成运行时回退。Tab 补全仍经 Agent Adapter 发单轮模型请求。
2. **技能**：Git 主仓完整技能目录 → R2 不可变包 → 登录网关 → 校验后的本地投影；不再经 Langfuse 分发技能。
3. **任务**：写作类工作在主会话按 Skill 执行；普通 Gallery 图像/音频/视频从 `lunaverse_produce` 开始，付费准入和去重由宿主管理。
4. **数据**：新作品使用 `books/<bookId>/`；正式剧本、素材、音频、小游戏各有规范目录。旧路径只承担兼容输入角色，不应成为新输出模板。
5. **状态**：作品步骤状态、Agent 活跃状态、生产 Job 状态、发布状态是不同事实，不互相代替。
6. **发布**：统一登录令牌访问 IDE 网关，上传后仍需检查版本绑定、readiness、activation 与 runtime truth。

## 维护与排障入口

- 普通使用以登录界面为入口，不要求成员寻找供应商密钥、Langfuse 管理员密钥或共享 admin cookie。
- 修改 Skill 改权威源码目录，检查并提交后由 IDE 仓既定发布流程处理；不要手改本地受管理缓存来冒充发布。
- 改 Wiki 不会触发 IDE 构建、技能发布或线上部署。
- 当前默认 Node 至少 22、包管理器固定 `pnpm 10.26.2`；精确检查与完整打包的区别见运维页。
- 本次未操作已安装 App、生产数据库、付费模型、发版流水线；源仓检查的失败也会保留，不能称“全绿”。

## 分专题阅读

- [[concepts/lunaverse-ide-ai-integration]]：Pi、登录、模型路由、Tab 与权限。
- [[concepts/lunaverse-ide-skills-and-production]]：全部技能的交付链与生产准入。
- [[concepts/agent-manuals-agents-md]]：产品规则、领域 Skill、本书偏好的区别。
- [[concepts/assets-produce-ide-workspace-contract]]：书籍目录、映射与数据恢复边界。
- [[concepts/assetctl-integration-contract]]：原子 CLI 当前注册表和退役能力。
- [[concepts/style-langfuse-migration]]：风格包与遗留 Langfuse 支持的准确边界。
- [[concepts/lunaverse-ide-creator-progress]]：步骤完成、审查报告和局部任务。
- [[concepts/lunaverse-ide-release-and-operations]]：发布、恢复、CLI、构建与验收。
- [[syntheses/lunaverse-ide-calibration-2026-09]]：全量覆盖账本、源仓问题及未验证项。

2026-05/06 的逐提交历史不再作为本页操作指南；可从 [校准前版本](https://github.com/cdotlock/mob-wiki/blob/20b102e7a516d6f15cb66383a8c98eed4be4d68a/wiki/entities/lunaverse-ide.md) 查阅。保留的历史专题均已标注适用日期。

## 核对来源

- [README.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/README.md)
- [package.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/package.json)
- [fork/build.mjs](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/fork/build.mjs)
- [scripts/check-repository-migration.mjs](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/scripts/check-repository-migration.mjs)
- [services/ide-cloud/README.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/services/ide-cloud/README.md)
- [agents/_shared/product/product-capabilities.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/_shared/product/product-capabilities.json)
- [docs/releases/ide-2.0.3/release-candidate.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/docs/releases/ide-2.0.3/release-candidate.json)
- [本次原始核对记录](../../raw/2026-09-15-lunaverse-ide-main-calibration.md)
- [补充验证与最终主线差异](../../raw/2026-09-15-lunaverse-ide-calibration-verification.md)
- [最终主线与开发指南撤回](../../raw/2026-09-15-lunaverse-ide-calibration-final-main.md)
