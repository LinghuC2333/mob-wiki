# IDE Wiki 校准附录 · 2026-09-15

初始固定证据：`MobAI-Inc/lunaverse-ide@14c089322ba14c06b65fcb17d9ecdfd4d807b8d3`。最终 main 校准到 `442fd53692d077c1e601231d79792c6c50b430e3`；两次完整增量见第 9、10 节，未变化的实现继续使用初始精确引用。

本文是完整性账本，不是所有条目的生产可用证明。Wiki 页经真实 MCP 读取；范围排查与事实验证分别标注。未改页没有被偷偷算作已复验。原始 raw 文件只追加本次记录，既有 raw 不修改。

## 1. Wiki 逐页处置（校准前基线）

| 页面 | 处置 | 理由与验证边界 |
|---|---|---|
| [Agent 手册机制 — per-agent AGENTS.md（codex 项目规则）](../wiki/concepts/agent-manuals-agents-md.md) | 现行正文重写 | 对照固定 main 的代码/manifest/合同修订；旧内容由 Git 历史保留。 |
| [中转站满血验机方法 + ECC 全局配置瘦身 playbook](../wiki/concepts/api-relay-verification-and-ecc-config-trim.md) | 范围排查后保留 | 通用验机/ECC 本机配置历史，非 IDE 产品源码；不改个人配置指导。 |
| [Asset Matting Hybrid (A 默认 + 检测 + B 兜底)](../wiki/concepts/asset-matting-hybrid.md) | 历史隔离 | 这是当时的特定素材管线、实验或事故记录，不是当前 IDE 的恢复/部署 runbook。现行注册表已移除独立 matting、upscale-image 等旧工具，透明角色生产已变化；不能据此批量重渲、删除缓存、部署旧远端服务或断言历史测试今天仍通过。 |
| [Asset Pipeline Aspect-Ratio Recovery (NRBI 2026-05)](../wiki/concepts/asset-pipeline-aspect-ratio-recovery-2026-05.md) | 历史隔离 | 这是当时的特定素材管线、实验或事故记录，不是当前 IDE 的恢复/部署 runbook。现行注册表已移除独立 matting、upscale-image 等旧工具，透明角色生产已变化；不能据此批量重渲、删除缓存、部署旧远端服务或断言历史测试今天仍通过。 |
| [Asset Pipeline — Green-Spill Root Cause + RGB Unspill Fix (2026-05-09)](../wiki/concepts/asset-pipeline-green-spill-fix-2026-05-09.md) | 历史隔离 | 这是当时的特定素材管线、实验或事故记录，不是当前 IDE 的恢复/部署 runbook。现行注册表已移除独立 matting、upscale-image 等旧工具，透明角色生产已变化；不能据此批量重渲、删除缓存、部署旧远端服务或断言历史测试今天仍通过。 |
| [Asset Pipeline — Green-Spill Runbook (recipes for follow-up runs)](../wiki/concepts/asset-pipeline-green-spill-runbook.md) | 历史隔离 | 这是当时的特定素材管线、实验或事故记录，不是当前 IDE 的恢复/部署 runbook。现行注册表已移除独立 matting、upscale-image 等旧工具，透明角色生产已变化；不能据此批量重渲、删除缓存、部署旧远端服务或断言历史测试今天仍通过。 |
| [to-final.py _raw Cache Trap](../wiki/concepts/asset-pipeline-to-final-raw-cache-trap-2026-05-10.md) | 历史隔离 | 这是当时的特定素材管线、实验或事故记录，不是当前 IDE 的恢复/部署 runbook。现行注册表已移除独立 matting、upscale-image 等旧工具，透明角色生产已变化；不能据此批量重渲、删除缓存、部署旧远端服务或断言历史测试今天仍通过。 |
| [assetctl — 原子能力 CLI 接口合同 v0.1.0 (assets-produce → lunaverse-ide)](../wiki/concepts/assetctl-integration-contract.md) | 现行正文重写 | 对照固定 main 的代码/manifest/合同修订；旧内容由 Git 历史保留。 |
| [assetctl skills sync + Block 2/3 staging（codex skill 加载链路）](../wiki/concepts/assetctl-skills-sync-and-staging.md) | 历史隔离 | Langfuse skill overlay、23 个旧 Skill、四个领域 Codex Home 及当时 push 状态均为历史。当前 Skill 走 Git → R2 完整包 → 认证网关 → 本地 receipt；文中旧授权/未推送说明不改变今天各仓的权限约定。 |
| [assets-produce ↔ Lunaverse IDE 工作区契约](../wiki/concepts/assets-produce-ide-workspace-contract.md) | 现行正文重写 | 对照固定 main 的代码/manifest/合同修订；旧内容由 Git 历史保留。 |
| [lunaverse-backend 生产安全加固（2026-06-10）](../wiki/concepts/backend-security-hardening-2026-06.md) | 范围排查后保留 | 独立 App Backend 安全加固记录，IDE 仓不能验证其生产安全。 |
| [CG Pipeline (07.5 step)](../wiki/concepts/cg-pipeline.md) | 历史隔离 | 这是上游旧生产阶段与脚本约定的历史快照。当前 IDE 的普通媒体生产先经宿主 lunaverse_produce；BGM/SFX、CG 形态、videoctl 与规范目录以当前专题为准。下文的阶段号、模型、文件路径和验收数字未作为现行合同复验。 |
| [CLI Gateway Protocol](../wiki/concepts/cli-gateway-protocol.md) | 跨域边界校准 | 本页通用远程 /exec 网关协议与 Lunaverse IDE Control 的本机实例路由、capability schema、request/job/receipt 协议是不同系统。不要拿旧 /exec bearer 或命令白名单替代 IDE 登录与宿主准入；通用网关当前部署未复验。 |
| [codex 运行时（IDE 内） — auth 模型 + 验证层级](../wiki/concepts/codex-runtime-and-verification-layers.md) | 历史隔离 | 旧 Codex 0.130 + Responses shim + CODEX_HOME 验证链已不是当前产品运行时。当前主 Agent 是 Pi，普通用户走统一登录；以下 env、binary 路径、L0–L2c 测试数字和已通结论只对原日期有效。不要从其他项目拷密钥或运行旧 smoke 来验证当前 IDE。 |
| [ComfyUI on Modal — matting + upscale-image serverless deploy](../wiki/concepts/comfyui-modal-deploy.md) | 历史隔离 | 这是当时的特定素材管线、实验或事故记录，不是当前 IDE 的恢复/部署 runbook。现行注册表已移除独立 matting、upscale-image 等旧工具，透明角色生产已变化；不能据此批量重渲、删除缓存、部署旧远端服务或断言历史测试今天仍通过。 |
| [DB 连接预算：Supavisor transaction 池 + 每引擎显式 cap](../wiki/concepts/db-connection-budget.md) | 范围排查后保留 | 独立 App Backend 数据库连接预算，未访问生产数据库。 |
| [Dream bonus_only OP + Feed Skip-to-E1](../wiki/concepts/dream-bonus-only-op.md) | 范围排查后保留 | Dream/推荐/玩家侧独立产品或后端设计；已排查主题关联，未据 IDE 仓复验其当前代码、模型、部署或业务数据。 |
| [dream-rec Component 1 — Bayesian TIRT estimator design](../wiki/concepts/dream-rec-component-1-tirt-estimator.md) | 范围排查后保留 | Dream/推荐/玩家侧独立产品或后端设计；已排查主题关联，未据 IDE 仓复验其当前代码、模型、部署或业务数据。 |
| [dream-rec Component 2 — LLM-as-annotator tagger](../wiki/concepts/dream-rec-component-2-llm-tagger.md) | 范围排查后保留 | Dream/推荐/玩家侧独立产品或后端设计；已排查主题关联，未据 IDE 仓复验其当前代码、模型、部署或业务数据。 |
| [dream-rec Component 3 — Genre projection design](../wiki/concepts/dream-rec-component-3-genre-projection.md) | 范围排查后保留 | Dream/推荐/玩家侧独立产品或后端设计；已排查主题关联，未据 IDE 仓复验其当前代码、模型、部署或业务数据。 |
| [dream-rec Component 4 — Dream ranker design](../wiki/concepts/dream-rec-component-4-dream-ranker.md) | 范围排查后保留 | Dream/推荐/玩家侧独立产品或后端设计；已排查主题关联，未据 IDE 仓复验其当前代码、模型、部署或业务数据。 |
| [dream-rec Component 5 — Cold-start questionnaire design](../wiki/concepts/dream-rec-component-5-cold-start.md) | 范围排查后保留 | Dream/推荐/玩家侧独立产品或后端设计；已排查主题关联，未据 IDE 仓复验其当前代码、模型、部署或业务数据。 |
| [dream-rec Component 6 — Three-Loop A/C/B Dashboard design](../wiki/concepts/dream-rec-component-6-dashboard.md) | 范围排查后保留 | Dream/推荐/玩家侧独立产品或后端设计；已排查主题关联，未据 IDE 仓复验其当前代码、模型、部署或业务数据。 |
| [dream-rec dev runbook](../wiki/concepts/dream-rec-dev-runbook.md) | 范围排查后保留 | Dream/推荐/玩家侧独立产品或后端设计；已排查主题关联，未据 IDE 仓复验其当前代码、模型、部署或业务数据。 |
| [dream-rec integration architecture (Component 0)](../wiki/concepts/dream-rec-integration-architecture.md) | 范围排查后保留 | Dream/推荐/玩家侧独立产品或后端设计；已排查主题关联，未据 IDE 仓复验其当前代码、模型、部署或业务数据。 |
| [dream-rec monorepo migration (2026-05-24)](../wiki/concepts/dream-rec-monorepo-migration.md) | 范围排查后保留 | Dream/推荐/玩家侧独立产品或后端设计；已排查主题关联，未据 IDE 仓复验其当前代码、模型、部署或业务数据。 |
| [dream-rec Paper](../wiki/concepts/dream-rec-paper2-plan.md) | 范围排查后保留 | Dream/推荐/玩家侧独立产品或后端设计；已排查主题关联，未据 IDE 仓复验其当前代码、模型、部署或业务数据。 |
| [dream-rec Ranker Upgrade — Optional Channels + Thompson Sampling (2026-06)](../wiki/concepts/dream-rec-ranker-upgrade-2026-06.md) | 范围排查后保留 | Dream/推荐/玩家侧独立产品或后端设计；已排查主题关联，未据 IDE 仓复验其当前代码、模型、部署或业务数据。 |
| [Dream Trigger v2 ↔ dream-rec Coexistence](../wiki/concepts/dream-rec-trigger-v2-coexistence.md) | 范围排查后保留 | Dream/推荐/玩家侧独立产品或后端设计；已排查主题关联，未据 IDE 仓复验其当前代码、模型、部署或业务数据。 |
| [Dream Trigger v2 — Mechanical Evaluator (no LLM)](../wiki/concepts/dream-trigger-v2-mechanical.md) | 范围排查后保留 | Dream/推荐/玩家侧独立产品或后端设计；已排查主题关联，未据 IDE 仓复验其当前代码、模型、部署或业务数据。 |
| [Dreaming Universe](../wiki/concepts/dreaming-universe.md) | 范围排查后保留 | Dream/推荐/玩家侧独立产品或后端设计；已排查主题关联，未据 IDE 仓复验其当前代码、模型、部署或业务数据。 |
| [Episode Writer · BGM 策略 & music-normalizer 流程](../wiki/concepts/episode-writer-music-strategy.md) | 历史隔离 | 这是上游旧生产阶段与脚本约定的历史快照。当前 IDE 的普通媒体生产先经宿主 lunaverse_produce；BGM/SFX、CG 形态、videoctl 与规范目录以当前专题为准。下文的阶段号、模型、文件路径和验收数字未作为现行合同复验。 |
| [SKILL / CLI / MCP / API Four-Layer Philosophy](../wiki/concepts/four-layer-philosophy.md) | 跨域边界校准 | 四层方法论可以保留，但它不是绕过 IDE 宿主的授权。当前普通媒体必须从 lunaverse_produce 进入，写作在主会话按 Skill 执行；CLI/MCP/API 的存在不等于可任选接口重复发起付费工作。 |
| [角色表情插帧实施方案](../wiki/concepts/frame-interpolation-spec.md) | 历史隔离 | 这是当时的特定素材管线、实验或事故记录，不是当前 IDE 的恢复/部署 runbook。现行注册表已移除独立 matting、upscale-image 等旧工具，透明角色生产已变化；不能据此批量重渲、删除缓存、部署旧远端服务或断言历史测试今天仍通过。 |
| [Gateway-bypass + Origin lockdown cutover 2026-06-10](../wiki/concepts/gateway-bypass-and-origin-lockdown-2026-06-10.md) | 范围排查后保留 | 独立网关源站隔离事故，未改变或验收网络/防火墙。 |
| [IAP SKU 定价体系（官方权威版）](../wiki/concepts/iap-sku-pricing.md) | 范围排查后保留 | 玩家付费与平台定价，非 IDE manifest 的事实；未复验现行价格。 |
| [IDE Single-Use Invite Codes](../wiki/concepts/ide-invite-codes-single-use.md) | 历史隔离 | 这是六月 App Backend 中 IDE 邀请/限流的实现与上线记录；IDE Cloud 权威已迁至独立仓库。客户端仍有邀请登录和认证网关，但本次没有复验 Cloud 数据模型、额度默认、限流数字、未用邀请码数量或生产 SQL。不能照下文旧表/迁移指令维护当前 IDE 身份服务。 |
| [IDE Tool Gateway Concurrency Limits](../wiki/concepts/ide-tool-gateway-concurrency-limit.md) | 历史隔离 | 这是六月 App Backend 中 IDE 邀请/限流的实现与上线记录；IDE Cloud 权威已迁至独立仓库。客户端仍有邀请登录和认证网关，但本次没有复验 Cloud 数据模型、额度默认、限流数字、未用邀请码数量或生产 SQL。不能照下文旧表/迁移指令维护当前 IDE 身份服务。 |
| [Lunascripts (LS) 格式规范](../wiki/concepts/ls-format.md) | 历史隔离 | 下文为六月 LS 规范/迁移快照，不是当前作者手册。IDE 已消费 4.0.0：INNER_THOUGHT/inner_thought、MC 最近 look、作者 signal 全大写，并保留明确版本兼容；不可混合旧例子或据此改写存量内容。 |
| [LS Spec Redesign (2026-06-04)](../wiki/concepts/ls-spec-redesign-2026-06.md) | 历史隔离 | 下文为六月 LS 规范/迁移快照，不是当前作者手册。IDE 已消费 4.0.0：INNER_THOUGHT/inner_thought、MC 最近 look、作者 signal 全大写，并保留明确版本兼容；不可混合旧例子或据此改写存量内容。 |
| [Lunaria Web — Agent v2（渐进式技能加载 + 双模式 + 持久化大纲 + AI 写提示词）](../wiki/concepts/lunaria-web-agent-v2.md) | 跨域边界校准 | 这是 Lunaria Web 的 Agent v2 记录；桌面现为 Pi、宿主生产和完整 Skill 交付，不能以当时“覆盖 IDE 百分比”或双模式类比为今日能力证明。Web 的现行实现未在本次复验。 |
| [Lunaverse IDE — AI 集成架构(Cline)](../wiki/concepts/lunaverse-ide-ai-integration.md) | 现行正文重写 | 对照固定 main 的代码/manifest/合同修订；旧内容由 Git 历史保留。 |
| [V10 抠图遗漏 sharpen_alpha bug + 修复（2026-05-28）](../wiki/concepts/matting-v10-sharpen-alpha-bug-2026-05-28.md) | 历史隔离 | 这是当时的特定素材管线、实验或事故记录，不是当前 IDE 的恢复/部署 runbook。现行注册表已移除独立 matting、upscale-image 等旧工具，透明角色生产已变化；不能据此批量重渲、删除缓存、部署旧远端服务或断言历史测试今天仍通过。 |
| [Moonshort IDE UI/UX Audit + Fix Log (2026-06)](../wiki/concepts/moonshort-ide-uiux-audit-2026-06.md) | 历史隔离 | 这里只记录六月那次 UI 审计及热补，不表示今天安装版经过视觉、交互、可访问性复验。现行产品入口是 Library、统一 Agent、Gallery、Voice Casting、F Studio、Preview 与 Release Center；本次仅核对源码，未运行 UI 审计。 |
| [MP Cross-Signal Author Guidance](../wiki/concepts/mp-cross-signal-author-guidance.md) | 跨域边界校准 | 这份多人引擎/写作指引不拥有当前 IDE 的 LS 语法。新作者 signal 使用 v4 的全大写规则；文中引擎自动生成的 mp_* 状态不要误判为作者变量并批量改名。多人后端时序与实现未在本次复验。 |
| [NovelDreamArtifact — Dream-Only LLM Artifact Sidecar Table](../wiki/concepts/novel-dream-artifact.md) | 范围排查后保留 | Dream/推荐/玩家侧独立产品或后端设计；已排查主题关联，未据 IDE 仓复验其当前代码、模型、部署或业务数据。 |
| [Novel GameConfig — Per-Novel Attribute System](../wiki/concepts/novel-game-config.md) | 范围排查后保留 | App 玩家数值配置；IDE 未拥有后端该实现。 |
| [乙女逐剧本质量评分器设计（per-script quality gate）](../wiki/concepts/otome-script-quality-evaluator.md) | 跨域边界校准 | 本文的旧 Gate 专项前提已不能当成当前 LS/IDE 发布门。主线采用现役 reviewer Skill 与规范报告，机械存在性、质量结论、局部任务和发布是分开的事实；不得把本设计的指标草案硬接成所有任务的固定阻塞。 |
| [乙女小说写作 Benchmark 调研 + 自建指标草案（2026-06-04）](../wiki/concepts/otome-writing-benchmark-survey-2026-06.md) | 跨域边界校准 | 这是六月 benchmark 调研和指标草案，不是当前 IDE 的实际通过率或执行许可。此轮没有运行付费评测，现役审查和进度以当前 Skill/报告/任务范围为准。 |
| [Production Pipeline — Two-Phase IDE Submit + Admin Activate](../wiki/concepts/production-pipeline-two-phase.md) | 历史隔离 | 本页保留 2026-05 App Backend 的 submit/admin-activate 设计。当前 IDE 客户端使用统一 JWT 的 /api/ide 路由与 Cloud workflow/readiness/fenced activation，不再只有四种状态；不要复用旧 admin cookie、SQL 或 activate 命令。独立 App Backend 当前实现/线上状态未在本次审计。 |
| [Railway Production Deploy + Zero-Data-Loss Cutover](../wiki/concepts/railway-production-deploy.md) | 范围排查后保留 | App Backend 旧部署/数据库 runbook，不能据 IDE 检查更新线上结论。 |
| [Remix Anywhere — Player Intervention via D20+DC Patch Injection](../wiki/concepts/remix-anywhere.md) | 范围排查后保留 | 玩家 Remix 与后端 overlay，未校验消费者当前实现或存量状态。 |
| [Second-Chorus 素材流水线（自包含 / 云端可跑 / 可复用模板）](../wiki/concepts/second-chorus-asset-pipeline.md) | 历史隔离 | 这是当时的特定素材管线、实验或事故记录，不是当前 IDE 的恢复/部署 runbook。现行注册表已移除独立 matting、upscale-image 等旧工具，透明角色生产已变化；不能据此批量重渲、删除缓存、部署旧远端服务或断言历史测试今天仍通过。 |
| [mobai-agent Server Layer](../wiki/concepts/server-layer.md) | 跨域边界校准 | 此页是 mobai-agent HTTP/WebSocket server，不是 IDE 内置 Pi runtime host。不要把它的端口、会话 schema 或 Web UI 当作 Lunaverse IDE 的启动条件；独立服务未在本次复验。 |
| [SFX Pipeline Design](../wiki/concepts/sfx-pipeline.md) | 历史隔离 | 这是上游旧生产阶段与脚本约定的历史快照。当前 IDE 的普通媒体生产先经宿主 lunaverse_produce；BGM/SFX、CG 形态、videoctl 与规范目录以当前专题为准。下文的阶段号、模型、文件路径和验收数字未作为现行合同复验。 |
| [Backend Support for LS `@signal int`](../wiki/concepts/signal-int-backend.md) | 跨域边界校准 | IDE v4 规范要求作者 mark/int signal 为 SCREAMING_SNAKE_CASE；小写引擎值独立保留。下文后端示例/存储实现属原日期，不能把旧作者小写变量直接复制到新剧本，也不能据 IDE 测试推断后端 rollout 完成。 |
| [Stable Step ID & Content-Addressed Cursor](../wiki/concepts/stable-step-id.md) | 跨域边界校准 | 当前 LS 4.0 rollout 明确保留既有 step ID tags 和 MC staging 标识，存量内容 audit_only。此处早期 ID/cursor 设计与迁移记录保留；不能因内心独白更名而重铸已发布步骤或改玩家存档。 |
| [Style prompts → Langfuse 权威源迁移（2026-06-02）](../wiki/concepts/style-langfuse-migration.md) | 现行正文重写 | 对照固定 main 的代码/manifest/合同修订；旧内容由 Git 历史保留。 |
| [Supabase Postgres Bootstrap + Migration Strategy](../wiki/concepts/supabase-backend-bootstrap.md) | 范围排查后保留 | App 数据库初始化/迁移历史，未调用 DB 或迁移命令。 |
| [Unfolded 风格互动视觉小说](../wiki/concepts/unfolded-visual-novel.md) | 范围排查后保留 | 展示形态/产品设计背景；不是当前 IDE 操作合同。 |
| [Villain Season — Heart Signal Otome Demo](../wiki/concepts/villain-season-demo.md) | 范围排查后保留 | 特定历史 demo 的生产/素材/玩家验证记录，不外推新版本通过。 |
| [Agent-Forge](../wiki/entities/agent-forge.md) | 跨域边界校准 | 此页是独立 Agent-Forge 平台记录。当前 IDE 的素材入口、Skill 源和运行时已在 IDE 仓，不能把此页 48 MCP 工具或旧 Agent Loop 当成 IDE 现役工具清单；原平台未在本次复验。 |
| [Assets-Produce](../wiki/entities/assets-produce.md) | 跨域边界校准 | 当前 IDE Skill 的 authoring authority 已在 IDE agents/**/skills/**；素材执行以宿主、现役 CLI 和完整 Skill 包为准。本页独立 assets-produce 产品与当时迁移/冻结记录保留，不代表今天要先启动该平台才能使用 IDE。 |
| [CLI Gateway](../wiki/entities/cli-gateway.md) | 跨域边界校准 | 这里的独立 /exec 服务不是当前 IDE Control CLI，也不是 IDE Cloud 模型/技能网关。四套旧部署与命令集仍为历史来源记录，不能用于判断当前桌面能力或在线状态。 |
| [Dramatizer-LS](../wiki/entities/dramatizer-ls.md) | 跨域边界校准 | 当前 IDE 自带并发布领域 Skill；不得按本页早期上游搬迁链寻找现役手册或直接复用旧脚本目录。原项目的历史管线与实验结果保留，本次没有独立复验该仓现状。 |
| [Dramatizer](../wiki/entities/dramatizer.md) | 跨域边界校准 | 当前团队桌面创作入口为 Lunaverse IDE 的统一 Agent/Skill/宿主生产链；这份独立 Dramatizer 的 15-stage/CLI/MCP 文档不应被当成 IDE 必需架构。原项目实现和在线服务未在本次复验。 |
| [lunaria-web](../wiki/entities/lunaria-web.md) | 跨域边界校准 | Lunaria Web 与桌面 IDE 分属不同实现；本页七月的能力对比、共享网关试验和已关闭 gap 不能外推到当前 Pi/Cloud/Skill/发布协议。Web 本身当前部署未在本次复验。 |
| [Lunascripts (LS) Interpreter](../wiki/entities/lunascripts.md) | 跨域边界校准 | 当前 IDE 消费上游 Lunascripts 4.0.0 的精确快照，已核对完整 vendor 树；新剧本为 .ls，内心独白为 INNER_THOUGHT。下文保留较早独立解释器介绍，旧命令/JSON 示例与部署测试不是当前 IDE 的完整合同。 |
| [Lunaverse Backend](../wiki/entities/lunaverse-backend.md) | 跨域边界校准 | App Backend 与 IDE Cloud 现在是不同拥有者。桌面仓明确声明 IDE Cloud 源码/部署权威为 cdotlock/lunaverse-ide-cloud；当前 IDE 发布客户端走 /api/ide 的统一登录与 workflow 协议。下文 App 数据模型、路由数、限流/邀请、部署与生产状态没有用 IDE 仓替代复验。 |
| [Lunaverse Client](../wiki/entities/lunaverse-client.md) | 跨域边界校准 | IDE 当前消费 LS 4.0.0，但不能据此认定这份独立 Cocos Client 或其线上安装已兼容。v3/v4、you/inner_thought 与有效发布身份须由消费者代码和部署验收证明；以下客户端阶段、事件与历史测试不在本次实现复验范围。 |
| [Lunaverse IDE](../wiki/entities/lunaverse-ide.md) | 现行正文重写 | 对照固定 main 的代码/manifest/合同修订；旧内容由 Git 历史保留。 |
| [Mob AI Router](../wiki/entities/mob-ai-router.md) | 跨域边界校准 | IDE 当前主 Agent 是 Pi，经统一登录网关和认证模型目录选择原生协议，不再沿用本页历史 Codex/shim 调用方描述。下文 router 模型清单、余额错误、端点和在线 smoke 属原日期，未据 IDE 仓证明今天仍可用。 |
| [mob-mini-agent](../wiki/entities/mob-mini-agent.md) | 范围排查后保留 | 独立 Pi foundation，不能因同用 Pi 就视作 IDE 同一运行库。 |
| [Mob Sandbox Ops](../wiki/entities/mob-sandbox-ops.md) | 范围排查后保留 | 独立 sandbox 运维；未操作服务、电源、SSH 或用户凭据。 |
| [mobai-agent](../wiki/entities/mobai-agent.md) | 跨域边界校准 | 独立 mobai-agent 主调度器与 IDE 内置 Lunaverse Agent 不是同一运行实现。IDE 当前由 ls-agent/agent-runtime 承载 Pi；本页 11 builtin tools 与旧 config.yaml 不能作为当前 IDE 工具/配置清单。 |
| [Vibe Motion](../wiki/entities/vibe-motion.md) | 范围排查后保留 | Remotion 营销作品与 IDE 历史素材引用，不是 IDE 产品功能权威。 |
| [Video Agent Claude Wangbo](../wiki/entities/video-agent-claude-wangbo.md) | 范围排查后保留 | 独立视频 prompt 实验项目，不是 IDE 当前 videoctl 的唯一 source。 |
| [Wiki Index](../wiki/index.md) | 导航/审计更新 | 目录描述和新增专题同步；日志追加逐页操作，不改历史日志。 |
| [Operation Log](../wiki/log.md) | 导航/审计更新 | 目录描述和新增专题同步；日志追加逐页操作，不改历史日志。 |
| [团队行动计划](../wiki/plan.md) | 跨域边界校准 | 新增本轮 IDE Wiki 校准的独立覆盖账本。下文旧全团队计划、负责人、已完成/待办状态未被本轮自动重置；IDE 当前待修项与未验收边界见校准报告，不把本轮 Wiki 完成等同其他团队任务完成。 |
| [Cloud Deployment Architecture](../wiki/syntheses/cloud-deployment-architecture.md) | 跨域边界校准 | 本页是早期平台分布式部署方案，不是当前 IDE Cloud 拓扑。IDE Cloud 已有独立权威仓，桌面、Router、Skill、Cloud、App Backend、更新组件分别拥有发布身份；本次未验证这些线上部署。 |
| [Data-Silence 失败类（VN Pipeline · 作者漏 render 家族）](../wiki/syntheses/data-silence-failure-class.md) | 历史隔离 | 这些是历史创作管线的失效模式与审查经验，不是当前 IDE 固定 gate 集或每次必须重跑的流程。经验可以迁移，具体审核、产物、语言和独立 reviewer 要求须读取现役 Skill 与当前书版本；未复验历史样本数字。 |
| [Lunaverse 全量改名迁移方案（lunaverse → Lunaverse / LS → Lunascripts / .ls → .ls）](../wiki/syntheses/lunaverse-rename-migration.md) | 历史隔离 | 本页是六月改名方案，且正文曾被机械替换，出现 .ls→.ls、同名仓库→同名仓库等失真映射，不能作为可执行改名表。当前桌面权威是 MobAI-Inc/lunaverse-ide，语言工具 lsc、规范后缀 .ls、书籍 books/<id>；旧 raw 保留作历史证据，不再执行本页的跨仓迁移、DB 列/存档重命名。 |
| [MobAI 平台全景指南](../wiki/syntheses/platform-onboarding-guide.md) | 跨域边界校准 | 团队创作者的当前桌面入口已是 Lunaverse IDE：Library → 主 Agent/Skill → Gallery/Voice → Preview/Release Center。下文 Dramatizer/Agent-Forge 全景是较早平台说明；玩家经济、推荐、收费与线上流程没有在本次对照 IDE 的校准中复验。 |
| [产品战略决策记录](../wiki/syntheses/product-strategy-decisions.md) | 跨域边界校准 | 本页保留战略决策理由，不作为实时服务清单。技术实现侧现行创作入口见 Lunaverse IDE；不能因战略里引用 Dramatizer/Agent-Forge 就推断桌面仍使用旧四 Agent 管线，也不据此重写历史产品决策。 |
| [Render-Time Silent Drop 失败类（VN Pipeline v4.1-v4.11 同构族）](../wiki/syntheses/render-time-silent-drop-failure-class.md) | 历史隔离 | 这些是历史创作管线的失效模式与审查经验，不是当前 IDE 固定 gate 集或每次必须重跑的流程。经验可以迁移，具体审核、产物、语言和独立 reviewer 要求须读取现役 Skill 与当前书版本；未复验历史样本数字。 |
| [publish-report CLI](../wiki/tools/publish-report.md) | 范围排查后保留 | 独立报告发布工具，与 IDE 作品发布无关。 |

基线 87 页，处置分布：现行正文重写 6；范围排查后保留 32；历史隔离 23；跨域边界校准 24；导航/审计更新 2。

新增当前专题：
- [concepts/lunaverse-ide-creator-progress](../wiki/concepts/lunaverse-ide-creator-progress.md)
- [concepts/lunaverse-ide-ls-contract](../wiki/concepts/lunaverse-ide-ls-contract.md)
- [concepts/lunaverse-ide-release-and-operations](../wiki/concepts/lunaverse-ide-release-and-operations.md)
- [concepts/lunaverse-ide-skills-and-production](../wiki/concepts/lunaverse-ide-skills-and-production.md)
- [syntheses/lunaverse-ide-calibration-2026-09](../wiki/syntheses/lunaverse-ide-calibration-2026-09.md)

## 2. 源仓覆盖矩阵

| 核对面 | 主要权威 | 证据层与边界 |
|---|---|---|
| 仓库/组件归属 | [scripts/check-repository-migration.mjs](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/scripts/check-repository-migration.mjs)<br>[services/ide-cloud/README.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/services/ide-cloud/README.md) | 远端 exact SHA + 发布源检查；独立 Cloud 仓内部未审计 |
| 产品入口/包/配置 | [agents/_shared/product/product-capabilities.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/_shared/product/product-capabilities.json)<br>[fork/build.mjs](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/fork/build.mjs)<br>[package.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/package.json) | manifest 与实现接线；未运行 UI 视觉/可访问性审计 |
| Pi/登录/模型/Tab | [packages/agent-runtime/src/runtime-selector.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/agent-runtime/src/runtime-selector.ts)<br>[packages/ls-agent/src/runtime-config.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-agent/src/runtime-config.ts)<br>[packages/ls-workbench/src/inline-completion.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-workbench/src/inline-completion.ts) | 现役调用代码与静态测试；未实发模型请求 |
| 技能完整包与投影 | [scripts/ide-skill-release.mjs](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/scripts/ide-skill-release.mjs)<br>[packages/ls-workbench/src/cline-skill-projection.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-workbench/src/cline-skill-projection.ts) | 33/33 本地检查；未验线上 pointer/新设备 |
| 生产/风格/原子工具 | [packages/ls-agent/src/production-mcp.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-agent/src/production-mcp.ts)<br>[vendor/assetctl/internal/tools/registry.go](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/vendor/assetctl/internal/tools/registry.go)<br>[packages/ls-workshop/src/bundled-style-packs.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-workshop/src/bundled-style-packs.ts) | 宿主准入、23 注册项、3 新选择包；未调用付费 provider |
| 书籍/映射/状态 | [packages/shared/src/node/book-layout.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/shared/src/node/book-layout.ts)<br>[packages/ls-workshop/src/local-mapping.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-workshop/src/local-mapping.ts)<br>[packages/shared/src/node/creator-step-progress.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/shared/src/node/creator-step-progress.ts) | 静态路径/归约核对；原本地脏编辑排除 |
| LS 与消费者 | [scripts/check-lunascripts-authority.mjs](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/scripts/check-lunascripts-authority.mjs)<br>[vendor/lunascripts/contract/contract.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/vendor/lunascripts/contract/contract.json) | 完整上游树与两份镜像通过；消费者上线未验 |
| Preview/声音/小游戏/CG | [packages/ls-preview/package.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-preview/package.json)<br>[agents/audio/knowledge/audio-production-contract.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/audio/knowledge/audio-production-contract.md)<br>[agents/minigame/cli/bindings.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/minigame/cli/bindings.json)<br>[agents/manga-cg/cli/bindings.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/manga-cg/cli/bindings.json) | 产品能力与输入输出绑定；未安装版播放/试听/生成 |
| 发布/快照恢复 | [packages/ls-preview/src/production-release-client.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-preview/src/production-release-client.ts)<br>[packages/ls-preview/src/project-snapshot-client.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-preview/src/project-snapshot-client.ts)<br>[packages/ls-preview/src/project-snapshot-restore.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-preview/src/project-snapshot-restore.ts) | 客户端协议与完整性；未改变 Cloud/App release |
| 外部 CLI/能力清单 | [packages/ide-control/src/cli-args.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ide-control/src/cli-args.ts)<br>[config/ide-control-capability-inventory.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/config/ide-control-capability-inventory.json) | 帮助/296 登记操作 + scanner 两个缺项；不是全部可无人值守运行 |
| 构建/分发/更新 | [.github/workflows/xcode-cloud-release.yml](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/.github/workflows/xcode-cloud-release.yml)<br>[.github/workflows/windows-release.yml](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/.github/workflows/windows-release.yml)<br>[.github/workflows/desktop-feed-promote.yml](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/.github/workflows/desktop-feed-promote.yml) | 工作流源与候选；未 dispatch/完整构建/安装升级 |
| 观测/运行诊断 | [docs/runbooks/agent-observability-laminar.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/docs/runbooks/agent-observability-laminar.md)<br>[packages/ls-agent/package.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-agent/package.json) | 脱敏命令与旧候选边界；未查看私有 traces/凭据 |
| 测试/文档漂移 | [test/agent-guidance-contract.test.mjs](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/test/agent-guidance-contract.test.mjs)<br>[scripts/check-guidance.mjs](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/scripts/check-guidance.mjs)<br>[docs/runbooks/final-e2e.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/docs/runbooks/final-e2e.md) | 实际结果如报告；不是根 check、Go/Vitest/GUI 全量验收 |

## 3. 全部顶层包、命令与配置快照

只读取仓库 manifest 的声明和默认值，不读取用户 settings 或环境值。显示名称可能保留兼容文案；是否运行以实际接线为准。

### packages/agent-adapter

[packages/agent-adapter/package.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/agent-adapter/package.json)；name `@lunaverse-ide/agent-adapter`；manifest version `0.1.0`。此版本不自动等于桌面发版号。

### packages/agent-runtime

[packages/agent-runtime/package.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/agent-runtime/package.json)；name `@lunaverse-ide/agent-runtime`；manifest version `0.1.0`。此版本不自动等于桌面发版号。

### packages/ide-control

[packages/ide-control/package.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ide-control/package.json)；name `ide-control`；manifest version `2.0.3`。此版本不自动等于桌面发版号。

| 命令 ID | 显示用途 |
|---|---|
| `lunaverse.control.installCli` | Install Lunaverse CLI |
| `lunaverse.control.configurePolicy` | Configure IDE Control Policy |

| 设置 | 完整声明（类型、默认、枚举、范围、说明） |
|---|---|
| `lunaverse.control.allowLoopbackGateway` | {"type": "boolean", "default": false, "scope": "machine", "description": "Allow an explicitly configured user-global Lunaverse gateway to use HTTP on localhost or 127.0.0.0/8 for local development. Workspace values are rejected."} |

### packages/ls-agent

[packages/ls-agent/package.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-agent/package.json)；name `ls-agent`；manifest version `0.1.0`。此版本不自动等于桌面发版号。

| 命令 ID | 显示用途 |
|---|---|
| `lunaverse.agent.open` | Open Agent / 打开 Agent |
| `lunaverse.agent.focusInput` | Focus Agent Input / 聚焦 Agent 输入框 |
| `lunaverse.agent.prefillInput` | Prefill Agent Input / 填入 Agent 任务 |
| `lunaverse.agent.newConversation` | New Agent Conversation / 新建 Agent 对话 |
| `lunaverse.agent.showHistory` | Agent Conversation History / Agent 对话历史 |
| `lunaverse.agent.restoreFiles` | Restore Agent Files / 恢复 Agent 文件 |
| `lunaverse.agent.copyDiagnostics` | Copy Sanitized Agent Diagnostics / 复制脱敏 Agent 诊断 |
| `lunaverse.agent.selectEffort` | Select Agent Reasoning / 选择 Agent 推理强度 |
| `lunaverse.agent.selectDagMode` | Select Agent Production Routing / 选择 Agent 生产路由 |
| `lunaverse.agent.addSelection` | Add Selection to Agent / 将选区加入 Agent |
| `lunaverse.agent.editSelection` | Edit Selection with Agent / 用 Agent 编辑选区 |
| `lunaverse.agent.explainSelection` | Explain Selection with Agent / 用 Agent 解释选区 |
| `lunaverse.agent.fixSelection` | Fix Selection with Agent / 用 Agent 修复选区 |

| 设置 | 完整声明（类型、默认、枚举、范围、说明） |
|---|---|
| `lunaverse.agent.pi.enabled` | {"type": "boolean", "default": true, "description": "Enable the only supported Pi Agent runtime. Disabling it makes Agent unavailable and never falls back to a legacy runtime. / 启用唯一受支持的 Pi Agent 运行时；关闭后 Agent 不可用且不会回退。"} |
| `lunaverse.agent.autoContinueWorkflows` | {"type": "boolean", "default": true, "scope": "resource", "description": "Automatically continue the owning Agent when a production Workflow reports a result. Disable this in workspaces where production is managed manually. / 生产工作流返回结果时自动续跑所属 Agent；手动管理生产的工作区可关闭。"} |
| `lunaverse.agent.pi.baseUrl` | {"type": "string", "default": "", "description": "Optional protocol endpoint override for Pi. Empty uses the signed-in Lunaverse gateway and the selected model's native protocol automatically. / Pi 可选协议端点覆盖；留空时自动复用当前 Lunaverse 网关及所选模型的原生协议。"} |
| `lunaverse.agent.pi.model` | {"type": "string", "default": "gpt-5.6-sol", "description": "Model used by the Pi Agent runtime. / Pi Agent 运行时使用的模型。"} |
| `lunaverse.agent.pi.effort` | {"type": "string", "enum": ["low", "medium", "high", "xhigh", "max"], "default": "high", "description": "Pi Agent reasoning effort; changes apply only at a turn boundary. / Pi Agent 推理强度，仅在轮次边界生效。"} |
| `lunaverse.agent.pi.permissionProfile` | {"type": "string", "enum": ["autonomous", "auto", "review", "plan"], "default": "auto", "description": "Agent permission profile: full host-managed autonomy, safe automatic creation, confirm sensitive actions, or plan without edits. / Agent 权限模式：宿主管理的完全自主、安全自动创作、敏感操作确认，或只规划不编辑。"} |
| `lunaverse.agent.pi.evalControllerPort` | {"type": "number", "default": 9878, "minimum": 1024, "maximum": 65535, "description": "Loopback port used only when the open workspace contains an authenticated evals.env fixture."} |

### packages/ls-lang

[packages/ls-lang/package.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-lang/package.json)；name `ls-lang`；manifest version `0.1.0`。此版本不自动等于桌面发版号。

| 设置 | 完整声明（类型、默认、枚举、范围、说明） |
|---|---|
| `lunaverse.lsPath` | {"type": "string", "default": "", "description": "Path to the lsc compiler binary."} |
| `lunaverse.lspPath` | {"type": "string", "default": "", "description": "Path to the ls-lsp binary."} |

### packages/ls-preview

[packages/ls-preview/package.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-preview/package.json)；name `ls-preview`；manifest version `0.1.0`。此版本不自动等于桌面发版号。

| 命令 ID | 显示用途 |
|---|---|
| `lunaverse.preview.load` | Preview Episode / 预览本集 |
| `lunaverse.openPreview` | Open Preview Episode / 打开预览 |
| `lunaverse.openVaultGraph` | Open Vault Graph / 打开 Vault 图谱 |
| `lunaverse.projectSnapshot.restore` | Restore Complete Project Snapshot / 恢复完整工程快照 |

| 设置 | 完整声明（类型、默认、枚举、范围、说明） |
|---|---|
| `lunaverse.preview.backendBaseUrl` | {"type": "string", "default": "", "description": "Optional lunaverse-backend admin base URL used by Preview voice casting and Release Center."} |
| `lunaverse.preview.backendAdminCookie` | {"type": "string", "scope": "machine", "default": "", "description": "Optional machine-local noval_admin cookie value/header used to sync selected voices to lunaverse-backend."} |
| `lunaverse.preview.backendNovelId` | {"type": "string", "default": "", "description": "Optional backend Novel.id for Preview voice casting sync and Release Center."} |
| `lunaverse.preview.breezeApiKey` | {"type": "string", "scope": "machine", "default": "", "markdownDescription": "Local development only. Logged-in users route Breeze voice casting through the Lunaverse gateway and do not need this key. Never commit it."} |
| `lunaverse.preview.playerUrl` | {"type": "string", "default": "", "markdownDescription": "Override URL of the Moonshort standalone Cocos player bundle used by the Preview toolbar's **真实播放器** toggle (renders an episode with the actual product renderer instead of the built-in React preview). Leave empty to use the vendored player that ships with the IDE (offline, renders local drafts). Set this only to point at a different deploy (e.g. a remote URL) — note a remote URL cannot load local-first draft assets."} |
| `lunaverse.publish.uploadConcurrency` | {"type": "integer", "default": 16, "minimum": 1, "maximum": 32, "markdownDescription": "How many release assets upload at once when publishing. Higher is faster on a fast connection. Uploads automatically retry on rate-limit/transient errors, so raising this self-throttles rather than failing a publish. Clamped to 1–32."} |

### packages/ls-studio

[packages/ls-studio/package.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-studio/package.json)；name `ls-studio`；manifest version `0.1.0`。此版本不自动等于桌面发版号。

| 命令 ID | 显示用途 |
|---|---|
| `lunaverse.studio.refresh` | Refresh F Studio / 刷新 F 工作室 |
| `lunaverse.studio.openClassic` | Switch to Classic editor / 切换到经典编辑器 |

### packages/ls-welcome

[packages/ls-welcome/package.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-welcome/package.json)；name `ls-welcome`；manifest version `0.1.0`。此版本不自动等于桌面发版号。

| 命令 ID | 显示用途 |
|---|---|
| `lunaverse.creator.openVisualEditor` | Visual edit / 可视化编辑 |
| `lunaverse.welcome.open` | Library / 书库 |
| `lunaverse.creator.focus` | Creator Navigator / 作品导航 |
| `lunaverse.creator.openProjectFiles` | Project Files / 项目文件 |
| `lunaverse.creator.useFriendlyMode` | Creator Workspace / 创作者版 |
| `lunaverse.creator.useProfessionalMode` | Professional Files / 专业文件 |
| `lunaverse.newProject` | New Project / 新建作品 |
| `lunaverse.openDemo` | Open Sample Book / 打开样板书 |
| `lunaverse.login` | Login / 登录 |
| `lunaverse.logout` | Logout / 退出登录 |
| `lunaverse.account.open` | Open Account / 打开账户 |
| `lunaverse.workspace.changeLocation` | Change Workspace Location / 更改作品存储位置 |
| `lunaverse.welcome.getStarted` | Get Started / 开始上手 |
| `lunaverse.language.select` | Switch Language / 切换界面语言 |
| `lunaverse.guidedTour.replay` | Replay Guided Tour / 重新运行新手指南 |

| 设置 | 完整声明（类型、默认、枚举、范围、说明） |
|---|---|
| `lunaverse.gatewayBaseUrl` | {"type": "string", "default": "https://ide-api.playlunaverse.com", "description": "Lunaverse gateway base URL. Legacy /v1 suffixes are normalized automatically."} |
| `lunaverse.authToken` | {"type": "string", "default": "", "description": "Legacy fallback token. Use Lunaverse: Login to store IDE tokens securely."} |
| `lunaverse.proxyUrl` | {"type": "string", "default": "", "description": "Override the proxy for Lunaverse gateway requests (e.g. http://127.0.0.1:7890). Usually unnecessary: when left empty the IDE connects directly and, if that fails, automatically routes through your detected system proxy (VS Code http.proxy / HTTPS_PROXY env / OS system proxy). Set this only to force a specific proxy."} |
| `lunaverse.showWelcomeOnStartup` | {"type": "boolean", "default": true, "deprecationMessage": "No longer used. The Lunaverse Library (bookshelf) now always shows on launch, except when you explicitly open a specific book or episode.", "description": "Deprecated and ignored. The Lunaverse Library page now always shows on every launch (except when you explicitly open a specific episode or book). This toggle no longer has any effect."} |
| `lunaverse.workspaceRoot` | {"type": "string", "default": "", "scope": "machine", "markdownDescription": "Lunaverse 作品资料库在这台电脑上的位置。留空时使用用户主目录下的 `Lunaverse IDE/workspace`。请通过 **Lunaverse: Change Workspace Location / 更改作品存储位置** 安全迁移已有资料，不要直接编辑此路径。 / Local storage location for this machine. Leave empty to use `Lunaverse IDE/workspace` under your home directory. Use the change-location command to migrate existing work safely instead of editing this path directly."} |
| `lunaverse.language` | {"type": "string", "enum": ["auto", "zh-CN", "en"], "enumItemLabels": ["跟随系统 / Follow system", "简体中文", "English"], "enumDescriptions": ["Follow the system / OS display language. 跟随操作系统语言。", "简体中文 Simplified Chinese.", "English."], "default": "auto", "scope": "application", "markdownDescription": "Lunaverse IDE 界面语言 / UI language for all Lunaverse surfaces (Library, Gallery, Preview, Studio, Account). Switching takes effect immediately across every Lunaverse panel. / 切换后 Lunaverse 各面板立即生效。"} |

### packages/ls-workbench

[packages/ls-workbench/package.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-workbench/package.json)；name `ls-workbench`；manifest version `0.1.0`。此版本不自动等于桌面发版号。

| 命令 ID | 显示用途 |
|---|---|
| `lunaverse.compile` | Compile Current LS / 编译当前剧本 |
| `lunaverse.validate` | Validate Current LS / 校验当前剧本 |
| `lunaverse.preview` | Preview Current LS / 预览当前剧本 |
| `lunaverse.generateAssetsMapping` | Generate Assets Mapping / 生成素材映射 |
| `lunaverse.publish` | Publish / 发布 |
| `lunaverse.openRawText` | Open Raw Text / 打开源文本 |
| `lunaverse.openStudio` | Switch to F Studio / 切换到 F Studio |

| 设置 | 完整声明（类型、默认、枚举、范围、说明） |
|---|---|
| `lunaverse.agent.provider` | {"type": "string", "default": "", "description": "Agent Adapter provider id (e.g. deepseek). Leave blank to fall back to the build-baked default. / Agent 适配器的供应商 id（如 deepseek）。留空则使用打包时内置的默认值。"} |
| `lunaverse.agent.baseUrl` | {"type": "string", "default": "", "description": "OpenAI-compatible API base URL for local-development Agent Adapter overrides. Normal users should use Lunaverse: Login. / 本地开发时覆盖 Agent 适配器用的 OpenAI 兼容 API 基础地址。普通用户请改用 Lunaverse: Login。"} |
| `lunaverse.agent.model` | {"type": "string", "default": "", "description": "Model id used by Tab completion and other Agent Adapter calls. Leave blank to fall back to the build-baked default. / Tab 补全及其他 Agent 适配器调用使用的模型 id。留空则使用打包时内置的默认值。"} |
| `lunaverse.agent.apiKey` | {"type": "string", "default": "", "markdownDescription": "Local-development fallback token for the Agent Adapter. Normal users should use **Lunaverse: Login**. Legacy `LUNAVERSE_AGENT_API_KEY` / `MOB_AI_KEY` / `MOB_AI_API_KEY` env vars are still read only for compatibility. **Never commit the token.** / Agent 适配器在本地开发时的备用 token。普通用户请改用 **Lunaverse: Login**。旧的 `LUNAVERSE_AGENT_API_KEY` / `MOB_AI_KEY` / `MOB_AI_API_KEY` 环境变量仅为兼容保留。**切勿把 token 提交到仓库。**"} |
| `lunaverse.inlineCompletion.enabled` | {"type": "boolean", "default": true, "description": "Enable or disable Tab predictive (ghost-text) completion for .ls files. When disabled, no AI requests are sent from the inline completion provider. / 启用或关闭 .ls 文件的 Tab 预测补全（幽灵文本）。关闭后，行内补全不会再发送任何 AI 请求。"} |

### packages/ls-workshop

[packages/ls-workshop/package.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-workshop/package.json)；name `ls-workshop`；manifest version `0.1.0`。此版本不自动等于桌面发版号。

| 命令 ID | 显示用途 |
|---|---|
| `lunaverse.openWorkshop` | Open Gallery / 打开画廊 |
| `lunaverse.openReleaseCenter` | Open Release Center / 打开发布中心 |
| `lunaverse.workshop.revealAsset` | Reveal Asset in Gallery / 在画廊中定位素材 |
| `lunaverse.workshop.recoverProductionDag` | Recover Interrupted Production Run |

| 设置 | 完整声明（类型、默认、枚举、范围、说明） |
|---|---|
| `lunaverse.styles.langfuse.publicKey` | {"type": "string", "scope": "machine", "default": "", "markdownDescription": "Langfuse public key for the production style catalog. Legacy `LANGFUSE_PUBLIC_KEY` / `LUNAVERSE_LANGFUSE_PUBLIC_KEY` env vars are still read as fallbacks."} |
| `lunaverse.styles.langfuse.secretKey` | {"type": "string", "scope": "machine", "default": "", "markdownDescription": "Langfuse secret key for editing and syncing the production style catalog. Store it in User Settings or machine-local workspace settings. **Never commit the token.**"} |
| `lunaverse.styles.langfuse.baseUrl` | {"type": "string", "default": "", "description": "Langfuse base URL for the production style catalog. Leave blank for https://cloud.langfuse.com."} |
| `lunaverse.styles.langfuse.label` | {"type": "string", "default": "", "description": "Langfuse label to read/write for production styles. Leave blank for production."} |
| `lunaverse.styles.langfuse.timeoutMs` | {"type": "number", "default": 5000, "minimum": 1, "description": "Timeout in milliseconds for Langfuse style catalog requests."} |
| `lunaverse.styles.langfuse.disabled` | {"type": "boolean", "default": false, "description": "Disable remote Langfuse style catalog loading and use the local/cache fallback."} |
| `lunaverse.agent.useIdeGateway` | {"type": "boolean", "scope": "machine", "default": true, "description": "Prefer the IDE backend model gateway over local provider routing when both a login token and a local provider key are available."} |
| `lunaverse.storage.r2.endpoint` | {"type": "string", "scope": "machine", "default": "", "description": "Cloudflare R2 S3-compatible endpoint used for generated asset uploads."} |
| `lunaverse.storage.r2.bucket` | {"type": "string", "scope": "machine", "default": "", "description": "Cloudflare R2 bucket used for generated asset uploads."} |
| `lunaverse.storage.r2.accessKeyId` | {"type": "string", "scope": "machine", "default": "", "markdownDescription": "Cloudflare R2 access key id. Legacy `R2_ACCESS_KEY_ID` env var is still read as a fallback."} |
| `lunaverse.storage.r2.secretAccessKey` | {"type": "string", "scope": "machine", "default": "", "markdownDescription": "Cloudflare R2 secret access key. Store it in User Settings or machine-local workspace settings. **Never commit the token.**"} |
| `lunaverse.storage.r2.publicBase` | {"type": "string", "scope": "machine", "default": "", "description": "Public HTTPS base URL that serves uploaded R2 assets."} |
| `lunaverse.storage.r2.region` | {"type": "string", "scope": "machine", "default": "auto", "description": "Cloudflare R2 region. Usually auto."} |

### packages/shared

[packages/shared/package.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/shared/package.json)；name `@lunaverse-ide/shared`；manifest version `0.1.0`。此版本不自动等于桌面发版号。

### services/ide-cloud

[services/ide-cloud/package.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/services/ide-cloud/package.json)；name `@lunaverse-ide/ide-cloud`；manifest version `0.1.0`。此版本不自动等于桌面发版号。

### services/ide-cloud-router

[services/ide-cloud-router/package.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/services/ide-cloud-router/package.json)；name `@lunaverse-ide/ide-cloud-router`；manifest version `0.1.0`。此版本不自动等于桌面发版号。

合计 manifest 显式命令 47 项、配置 40 项。私有命令/消息不在这个数字里，另见第 6 节。

## 4. 全部生产 Skill 源目录

| wire name | 源目录 | 文件数 |
|---|---|---|
| `adaptation-arc-reviewer` | [agents/adaptation/skills/arc-reviewer/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/adaptation/skills/arc-reviewer/SKILL.md) | 9 |
| `adaptation-bible-reviewer` | [agents/adaptation/skills/bible-reviewer/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/adaptation/skills/bible-reviewer/SKILL.md) | 5 |
| `adaptation-character-architect` | [agents/adaptation/skills/character-architect/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/adaptation/skills/character-architect/SKILL.md) | 7 |
| `adaptation-cover-spec` | [agents/adaptation/skills/cover-spec/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/adaptation/skills/cover-spec/SKILL.md) | 1 |
| `adaptation-entity-normalizer` | [agents/adaptation/skills/entity-normalizer/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/adaptation/skills/entity-normalizer/SKILL.md) | 8 |
| `adaptation-entity-planner` | [agents/adaptation/skills/entity-planner/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/adaptation/skills/entity-planner/SKILL.md) | 35 |
| `adaptation-entity-rename` | [agents/adaptation/skills/entity-rename/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/adaptation/skills/entity-rename/SKILL.md) | 33 |
| `adaptation-episode-writer` | [agents/adaptation/skills/episode-writer/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/adaptation/skills/episode-writer/SKILL.md) | 55 |
| `adaptation-episode-writer-reviewer` | [agents/adaptation/skills/episode-writer-reviewer/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/adaptation/skills/episode-writer-reviewer/SKILL.md) | 10 |
| `adaptation-experience-reviewer` | [agents/adaptation/skills/experience-reviewer/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/adaptation/skills/experience-reviewer/SKILL.md) | 2 |
| `adaptation-novel-evaluator` | [agents/adaptation/skills/novel-evaluator/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/adaptation/skills/novel-evaluator/SKILL.md) | 7 |
| `adaptation-novel-selector` | [agents/adaptation/skills/novel-selector/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/adaptation/skills/novel-selector/SKILL.md) | 16 |
| `adaptation-plan-vault` | [agents/adaptation/skills/plan-vault/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/adaptation/skills/plan-vault/SKILL.md) | 56 |
| `adaptation-planner-reviewer` | [agents/adaptation/skills/planner-reviewer/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/adaptation/skills/planner-reviewer/SKILL.md) | 36 |
| `adaptation-playthrough-reviewer` | [agents/adaptation/skills/playthrough-reviewer/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/adaptation/skills/playthrough-reviewer/SKILL.md) | 2 |
| `adaptation-rename-reviewer` | [agents/adaptation/skills/rename-reviewer/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/adaptation/skills/rename-reviewer/SKILL.md) | 2 |
| `asset-cg-render-spec` | [agents/asset/skills/cg-render-spec/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/asset/skills/cg-render-spec/SKILL.md) | 1 |
| `asset-character-portrait-spec` | [agents/asset/skills/character-portrait-spec/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/asset/skills/character-portrait-spec/SKILL.md) | 1 |
| `asset-character-portrait-v2` | [agents/asset/skills/character-portrait-v2/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/asset/skills/character-portrait-v2/SKILL.md) | 16 |
| `asset-character-visual-batch` | [agents/asset/skills/character-visual-batch/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/asset/skills/character-visual-batch/SKILL.md) | 1 |
| `asset-cover-spec` | [agents/asset/skills/cover-spec/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/asset/skills/cover-spec/SKILL.md) | 1 |
| `asset-ep-sprite-spec` | [agents/asset/skills/ep-sprite-spec/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/asset/skills/ep-sprite-spec/SKILL.md) | 1 |
| `asset-minimax-h3-cg-video-prompt` | [agents/asset/skills/minimax-h3-cg-video-prompt/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/asset/skills/minimax-h3-cg-video-prompt/SKILL.md) | 5 |
| `asset-outfit-anchor-spec` | [agents/asset/skills/outfit-anchor-spec/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/asset/skills/outfit-anchor-spec/SKILL.md) | 1 |
| `asset-scene-bg-spec` | [agents/asset/skills/scene-bg-spec/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/asset/skills/scene-bg-spec/SKILL.md) | 1 |
| `asset-seedance-2-cg-video-prompt` | [agents/asset/skills/seedance-2-cg-video-prompt/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/asset/skills/seedance-2-cg-video-prompt/SKILL.md) | 2 |
| `asset-shot-image-from-ls` | [agents/asset/skills/shot-image-from-ls/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/asset/skills/shot-image-from-ls/SKILL.md) | 1 |
| `audio-music-spec` | [agents/audio/skills/music-spec/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/audio/skills/music-spec/SKILL.md) | 1 |
| `audio-sfx-spec` | [agents/audio/skills/sfx-spec/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/audio/skills/sfx-spec/SKILL.md) | 1 |
| `human-writing` | [agents/_shared/skills/human-writing/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/_shared/skills/human-writing/SKILL.md) | 10 |
| `manga-cg-manga-cg-director` | [agents/manga-cg/skills/manga-cg-director/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/manga-cg/skills/manga-cg-director/SKILL.md) | 119 |
| `manga-cg-manga-cg-fast-lane` | [agents/manga-cg/skills/manga-cg-fast-lane/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/manga-cg/skills/manga-cg-fast-lane/SKILL.md) | 11 |
| `minigame-minigame-builder` | [agents/minigame/skills/minigame-builder/SKILL.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/agents/minigame/skills/minigame-builder/SKILL.md) | 9 |

数量为固定提交事实，live catalog 仍需登录后校验。script 有领域 manifest 但无 Skill，_shared 不是一个 Agent。

## 5. CLI 完整帮助与领域 bindings

[packages/ide-control/src/cli-args.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ide-control/src/cli-args.ts) 的帮助合同快照；不是执行授权：

```text
Usage: lunaverse <command> [options]

Start here:
  lunaverse doctor
  lunaverse --version
  lunaverse catalog
  lunaverse tools show <tool-id>
  lunaverse run <tool-id> --input <json|@file|->

Commands:
  lunaverse capabilities inventory
  lunaverse tools list
  lunaverse tools show <tool-id>
  lunaverse requests list
  lunaverse requests status <request-id>
  lunaverse jobs list
  lunaverse jobs status <job-id>
  lunaverse jobs watch <job-id>
  lunaverse jobs cancel <job-id>
  lunaverse events follow
  lunaverse flows list
  lunaverse flows show <flow-id>
  lunaverse flows plan <flow-id> --input <json|@file|->
  lunaverse flows start <flow-id> --input <json|@file|-> --request-id <stable-id>
  lunaverse ide list
  lunaverse ide status
  lunaverse auth status
  lunaverse policy show
  lunaverse policy configure
  lunaverse agent setup <codex|claude|pi> --scope <user|project> [--force]
  lunaverse trace check [codex|claude|pi]

Agent observability:
  Lunaverse IDE automatically detects installed Codex, Claude Code, and Pi
  clients and manages their official tracing plugins. Cloud export starts only
  after an authenticated Agent session actually calls Lunaverse IDE.
  agent setup remains available for immediate repair and guidance installation.

Options:
  --json                       Emit one JSON response envelope
  --jsonl                      Emit subscription events as JSON Lines
  --input <json|@file|->       Bounded JSON input for run
  --request-id <id>            Stable idempotency key for run or cancel
  --instance <id>              Select a running IDE instance
  --workspace <path>           Select a workspace
  --book <id>                  Select a book
  --after-sequence <number>    Resume a subscription cursor
  --cursor <cursor>            Resume a bounded job or request list page
  --scan-cursor <request-id>   Resume a budget-bounded request list scan
  --limit <number>             Bound a job or request list response (1-100)
  --state <state>              Filter jobs or requests by durable state
  --type <tool-id>             Filter jobs or requests by capability ID
  --topic <topic>              Scope events follow to one topic
  --job <job-id>               Scope events follow to one job
  --request <request-id>       Scope events follow to one request
  --scope <user|project>       Agent guidance scope; managed tracing is per-user
  --force                      Replace guidance; never foreign Laminar config
  --version                    Show running extension version, path, and commit
  -h, --help                   Show help

Machine contract:
  --json emits one JSON envelope on stdout. --jsonl emits one envelope per line.
  Requested results, including the human doctor report, are written to stdout.
  stderr is reserved for local human diagnostics; machine output never mixes prose.
  doctor is read-only at product level; discovery may initialize local runtime
  state and activate catalog owners.
  Exit codes: 0 success, 2 usage, 3 IDE offline, 4 auth/policy, 5 input,
              6 domain failure, 7 outcome unknown.
```

另有领域 CLI bindings；以下是源码候选目录，不授权绕过 lunaverse_produce。

| 领域 | binding | command/baseArgs | 语义 |
|---|---|---|---|
| adaptation | `ls-validate` | ["lsc", ["validate"]] | Validate each generated episode's LS before it is accepted. |
| adaptation | `assetctl` | ["agents/asset/cli/assetctl/bin/assetctl", []] | Atomic image-generation capability CLI for Novel Adaptation cover work. Use `assetctl tools list`, `assetctl tools show <id>`, and `assetctl run <id> --input <json\|@file>` for cover-spec tasks. Intended atoms here are generate-image-gpt (the image-generation standard) and hybrid-to-webp (the host requires cover.webp, so convert any png/jpg the generator returns). The cover is delivered as a LOCAL cover.webp — do not r2-put it; publishing is a separate user-driven step. Keep general asset production in Asset Production. |
| adaptation | `lunaverse-eval` | ["agents/adaptation/cli/lunaverse-eval/bin/lunaverse-eval", []] | Spoiler-safe CUI player for opt-in CLI playthrough review. Invoke one interactive PTY process with `"$LUNAVERSE_EVAL_BINARY" play --receipt <receipt.json> --trace <trace.json> [--episode <next.json> ...] [--stop-after-episode <episode_id>] <compiled-json>` and keep that process alive through the intended route. An explicitly scoped episode slice terminates as slice_completed and preserves the actual next episode; it is not a full release pass. missing_target is never successful completion. Compile `.ls` to JSON first via the `ls-validate` binary's `lsc compile` path. |
| asset | `ls-validate` | ["lsc", ["validate"]] | Validate LS syntax before asset planning. |
| asset | `videoctl` | ["agents/asset/cli/videoctl/bin/videoctl", []] | The host executor for CG and shot-video production orders: canonical prompt parsing, IDE-gateway submission, durable run/status, safe resume, canonical download, and frame extraction. The conversational Agent submits lunaverse_produce and must not invoke paid run-shot/submit directly. Never duplicate a request with an assetctl video atom, handwritten HTTP, or a direct provider call. Its authoritative Go source is vendor/videoctl and the packaged binary is staged at agents/asset/cli/videoctl/bin/videoctl. Usage and boundaries: cli/videoctl/docs/AGENT_REFERENCE.md. |
| asset | `assetctl` | ["agents/asset/cli/assetctl/bin/assetctl", []] | Atomic asset-capability CLI for capability discovery (`tools list` / `tools show <id>`) and JSON-envelope execution (`run <id> --input <json\|@file>`). Always use the host-provided `$ASSETCTL_BINARY`; do not call retired batch scripts or ask users for provider keys. Workshop CG and shot-video generation is intentionally absent and belongs exclusively to `$VIDEOCTL_BINARY`. In normal IDE login mode, paid and remote tools route through the IDE backend gateway using the current Lunaverse token. Use `generate-image-gpt` for image generation. Treat `tools list` and `tools show` as the current catalog and schema authority. If a paid atom reports a missing IDE token, ask the user to run Lunaverse: Login. If a route is unsupported, report that it must be deployed or configured; never bypass the gateway with local secrets. |
| audio | `ls-validate` | ["lsc", ["validate"]] | Validate LS syntax before audio planning. |
| audio | `assetctl` | ["agents/asset/cli/assetctl/bin/assetctl", []] | Host-side atomic audio executor. The conversational Agent must use lunaverse_produce instead of paid assetctl commands. The host uses generate-music-suno and generate-sfx-elevenlabs with canonical saveToPath under 08-audio-production; the verified local MP3 is the deliverable. |
| manga-cg | `manga-cg-runtime` | ["manga-cg-runtime", []] | Bootstrap, diagnose, or print the pinned Manga CG runtime paths. Run `manga-cg-runtime bootstrap` once after checkout, then `manga-cg-runtime doctor` before production. |
| manga-cg | `manga-cg-hyperframes` | ["manga-cg-hyperframes", []] | Run the pinned local Hyperframes 0.7.58 runtime through the Manga CG allowlist. Only local lint/check/snapshot/keyframes/compare/preview/render/doctor/browser/info/docs commands are exposed; network publishing, cloud, feedback, media-generation, and background-removal commands are refused. |
| manga-cg | `manga-cg-build` | ["manga-cg-build", []] | Bundle one Agent-authored ESM composition with the pinned local esbuild 0.25.12 into a browser IIFE. The final index.html must load that output with a plain script tag so inspector, snapshot, preview, and render execute identical bytes. |
| manga-cg | `resolve_lunascript_context.py` | ["resolve_lunascript_context.py", []] | Resolve one LunaScript @cg/@manga handle or standalone description into portable context.md/context.json before creative work begins. |
| manga-cg | `run_state.py` | ["run_state.py", []] | Initialize, advance, invalidate, and verify the non-skippable Manga CG production ledger. It enforces ordered stages, evidence shapes, hashes, and downstream invalidation without encoding creative choreography. |
| manga-cg | `validate_binding.py` | ["validate_binding.py", []] | Validate a Fast Lane binding against its published template hash, slot order, and layout family before art or compose. |
| manga-cg | `materialize_fast_composition.py` | ["materialize_fast_composition.py", []] | Copy published Fast Lane engine files into a run after binding is valid. Agents must not author composition.mjs. |
| manga-cg | `build_fast_bubble_tasks.py` | ["build_fast_bubble_tasks.py", []] | Build Fast Lane place_bubbles tasks from published template geometry and binding dialogue. compose-check.mjs runs this before YOLO placement. |
| manga-cg | `art_contracts.py` | ["art_contracts.py", []] | Build machine-owned Manga CG art requirements, compact approved-assets manifests, and the paired original/placement Gate S batch preview without recording reviews or mutating the production ledger. |
| manga-cg | `render_board_webp.mjs` | ["node", ["agents/manga-cg/skills/manga-cg-director/scripts/storyboard/render_board_webp.mjs"]] | Render a storyboard SVG containing real run-local source crops into a provenance-bound WebP candidate sheet, repairing common unescaped text characters before rasterization. |
| manga-cg | `render_placement_preview.py` | ["render_placement_preview.py", []] | Render a deterministic real 9:16 placement preview from one canonical Manga CG WebP using the planned focal point. |
| manga-cg | `build_stable_contact_sheet.py` | ["build_stable_contact_sheet.py", []] | Combine ordered label=path stable-hold screenshots into one numbered, deterministic contact sheet with contain-fit so edge and crop evidence is never hidden by the review artifact. |
| manga-cg | `inspect-composition.mjs` | ["node", ["agents/manga-cg/tools/manga-cg-sdk/scripts/inspect-composition.mjs"]] | Inspect the exact browser IIFE for pinned runtime/assets, declared stable holds, active-panel and boundary leakage, author-content coverage, protected focal survival, synchronized geometry, sampled transitions, bubble containment, stable canvas, and browser errors. Pass --stable-frame-dir to capture the same holds for blind visual preflight. |
| manga-cg | `compose-check.mjs` | ["node", ["agents/manga-cg/tools/manga-cg-sdk/scripts/compose-check.mjs"]] | Build, inspect, capture stable frames, assemble the contact sheet, render once, and finalize the post-profile Gate F candidate in one deterministic transaction. |
| manga-cg | `render-composition.mjs` | ["node", ["agents/manga-cg/tools/manga-cg-sdk/scripts/render-composition.mjs"]] | The one supported render entry for a manga-SDK composition. Inlines the gate-inspected *.iife.js bundle + GSAP into a self-contained entry and drives manga-cg-hyperframes render from the run root, so the composition's ../art references resolve and the muxed fps defaults to the build report's authored fps. Use instead of a raw manga-cg-hyperframes render, which 404s on ./dist/*.iife.js under a run-root cwd. |
| manga-cg | `build_tagged_evidence_boards.py` | ["build_tagged_evidence_boards.py", []] | Pack an Agent-selected 0-9 reference set into one or two labeled, identity-weighted evidence boards when a compact visual summary improves review or external asset handoff clarity. |
| manga-cg | `fetch_models.py` | ["fetch_models.py", []] | Repair or independently re-fetch the licensed face/head/saliency weights from the pinned registry and verify every SHA-256 into a chosen cache. Normal fresh checkouts use the bundled runtime/models files. |
| manga-cg | `place_bubbles.py` | ["place_bubbles.py", []] | Place exact editable bubble copy inside arbitrary simple panel polygons using hard licensed face/head exclusion, exact containment and forbidden regions, with saliency-aware soft scoring and an auditable top-level passing report. |
| manga-cg | `finalize_video.py` | ["finalize_video.py", []] | Mechanically finalize an approved 1080x1920/24fps Manga CG MP4 with an explicit page-safe matte or cinematic full-bleed delivery profile, encode H.264/yuv420p without changing timing, verify the result, and emit handle/hash metadata. Use --dry-run before writing. This command does not edit or visually review an existing CG. |
| minigame | `ls-validate` | ["lsc", ["validate"]] | Validate LS syntax before minigame replacement. |
| minigame | `minigamectl` | ["agents/minigame/cli/minigamectl/bin/minigamectl", []] | Deterministic Lunaverse H5 minigame builder. Use `run build --input <json\|@file>` with the host-provided absolute outDir `<bookRoot>/09-minigame-production/games`. It writes `<outDir>/<gameId>/index.html` and returns mappingEntry/lsSnippet metadata. Local files are the deliverable; registration and publishing are host actions. |
| minigame | `assetctl` | ["agents/asset/cli/assetctl/bin/assetctl", []] | Atomic image-generation and image-processing CLI used for Layer 2/3 minigame visuals. Inspect the current catalog/schema with `tools list` and `tools show <id>` before `run`. |

## 6. IDE Control 完整登记清单与已知缺项

下面 296 项是源仓已经登记的操作，不全是公开能力或无人值守命令；excluded/presentation-only 必须保留边界。源码 scanner 另发现两个未登记项，故该清单当前不能称无遗漏。

- 未登记 private-command：`lunaverse.ls-workshop / lunaverse.workshop.refreshStepProgress`。
- 未登记 webview-message：`lunaverse.ls-workshop / workshop.importStylePack`。

[config/ide-control-capability-inventory.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/config/ide-control-capability-inventory.json)

| 拥有者 | 类型 | 操作 | disposition | capability / alias |
|---|---|---|---|---|
| lunaverse.ls-preview | manifest-command | lunaverse.openPreview | presentation-only | — |
| lunaverse.ls-preview | manifest-command | lunaverse.openVaultGraph | presentation-only | — |
| lunaverse.ls-preview | manifest-command | lunaverse.preview.load | capability | preview.compile |
| lunaverse.ls-preview | manifest-command | lunaverse.projectSnapshot.restore | capability | snapshot.restore |
| lunaverse.ls-preview | private-command | lunaverse.preview.loadPrepared | covered-by-atom | preview.prepare |
| lunaverse.ls-preview | private-command | lunaverse.preview.monitorBeat | presentation-only | — |
| lunaverse.ls-preview | private-command | lunaverse.agent.ensureProductionAcceptance | excluded | — |
| lunaverse.ls-preview | private-command | lunaverse.preview.ensureProductionAcceptance | excluded | — |
| lunaverse.ls-preview | private-command | lunaverse.release.fetch | capability | release.fetch |
| lunaverse.ls-preview | private-command | lunaverse.release.bundle | capability | release.bundle |
| lunaverse.ls-preview | private-command | lunaverse.release.publish | covered-by-atom | release.publish |
| lunaverse.ls-preview | private-command | lunaverse.release.publishPinned | covered-by-atom | release.publish |
| lunaverse.ls-preview | private-command | lunaverse.release.scan | covered-by-atom | release.scan |
| lunaverse.ls-preview | private-command | lunaverse.release.workflow | excluded | — |
| lunaverse.ls-preview | webview-message | graph.open | covered-by-atom | file.open |
| lunaverse.ls-workshop | private-command | lunaverse.release.controlResult | presentation-only | — |
| lunaverse.ls-preview | webview-message | graph.ready | presentation-only | — |
| lunaverse.ls-preview | webview-message | graph.refresh | presentation-only | — |
| lunaverse.ls-preview | webview-message | preview.backToWorkshop | presentation-only | — |
| lunaverse.ls-preview | webview-message | preview.openReleaseCenter | presentation-only | — |
| lunaverse.ls-preview | webview-message | preview.ready | presentation-only | — |
| lunaverse.ls-studio | manifest-command | lunaverse.studio.openClassic | presentation-only | — |
| lunaverse.ls-studio | manifest-command | lunaverse.studio.refresh | presentation-only | — |
| lunaverse.ls-studio | webview-message | studio.compile | covered-by-atom | script.compile |
| lunaverse.ls-studio | webview-message | studio.generateAssetsMapping | covered-by-atom | asset.mapping-update |
| lunaverse.ls-studio | webview-message | studio.generateMinigame | excluded | — |
| lunaverse.ls-studio | webview-message | studio.openRawText | covered-by-atom | file.open |
| lunaverse.ls-studio | webview-message | studio.preview | capability | studio.preview-open |
| lunaverse.ls-studio | webview-message | studio.ready | presentation-only | — |
| lunaverse.ls-studio | webview-message | studio.requestAssetFiles | presentation-only | — |
| lunaverse.ls-studio | webview-message | studio.revealAsset | covered-by-atom | file.reveal |
| lunaverse.ls-studio | webview-message | studio.save | excluded | — |
| lunaverse.ls-studio | webview-message | studio.validate | covered-by-atom | script.validate |
| lunaverse.ls-welcome | manifest-command | lunaverse.account.open | presentation-only | — |
| lunaverse.ls-welcome | manifest-command | lunaverse.creator.focus | presentation-only | — |
| lunaverse.ls-welcome | manifest-command | lunaverse.creator.openProjectFiles | presentation-only | — |
| lunaverse.ls-welcome | manifest-command | lunaverse.creator.useFriendlyMode | presentation-only | — |
| lunaverse.ls-welcome | manifest-command | lunaverse.creator.useProfessionalMode | presentation-only | — |
| lunaverse.ls-welcome | manifest-command | lunaverse.language.select | presentation-only | — |
| lunaverse.ls-welcome | manifest-command | lunaverse.login | excluded | — |
| lunaverse.ls-welcome | manifest-command | lunaverse.logout | excluded | — |
| lunaverse.ls-welcome | manifest-command | lunaverse.newProject | excluded | — |
| lunaverse.ls-welcome | manifest-command | lunaverse.openDemo | excluded | — |
| lunaverse.ls-welcome | manifest-command | lunaverse.welcome.getStarted | presentation-only | — |
| lunaverse.ls-welcome | manifest-command | lunaverse.welcome.open | presentation-only | — |
| lunaverse.ls-welcome | manifest-command | lunaverse.workspace.changeLocation | excluded | — |
| lunaverse.ls-welcome | private-command | lunaverse.account.focus | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.account.refreshChip | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.agent.openWalkthrough | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.agent.setCreatorGuidanceContext | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.auth.getAccessToken | excluded | — |
| lunaverse.ls-welcome | private-command | lunaverse.auth.getState | capability | auth.status |
| lunaverse.ls-welcome | private-command | lunaverse.auth.getUsage | excluded | — |
| lunaverse.ls-welcome | private-command | lunaverse.auth.requireLogin | excluded | — |
| lunaverse.ls-welcome | private-command | lunaverse.creator.openAudio | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.creator.getAgentStatusContext | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.creator.openEpisodes | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.creator.openOverview | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.creator.openPreview | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.creator.openPublish | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.creator.openStory | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.creator.openVisuals | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.creatorDocument.open | capability | file.open |
| lunaverse.ls-welcome | private-command | lunaverse.creatorFiles.focus | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.creatorNavigator.focus | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.welcome.openBookWorkspace | excluded | — |
| lunaverse.ls-welcome | webview-message | account.changeWorkspaceLocation | excluded | — |
| lunaverse.ls-welcome | webview-message | account.login | excluded | — |
| lunaverse.ls-welcome | webview-message | account.logout | excluded | — |
| lunaverse.ls-welcome | webview-message | account.ready | covered-by-atom | auth.status |
| lunaverse.ls-welcome | webview-message | account.registerWithInvite | excluded | — |
| lunaverse.ls-welcome | webview-message | account.saveProductionConfig | covered-by-atom | production.config-update |
| lunaverse.ls-welcome | webview-message | account.setLanguage | presentation-only | — |
| lunaverse.ls-welcome | webview-message | creatorDocument.ready | presentation-only | — |
| lunaverse.ls-welcome | webview-message | creatorDocument.save | excluded | — |
| lunaverse.ls-welcome | webview-message | open | presentation-only | — |
| lunaverse.ls-welcome | webview-message | open-item | presentation-only | — |
| lunaverse.ls-welcome | webview-message | switch-mode | presentation-only | — |
| lunaverse.ls-welcome | webview-message | welcome.duplicateSample | excluded | — |
| lunaverse.ls-welcome | webview-message | welcome.intakeCancel | presentation-only | — |
| lunaverse.ls-welcome | webview-message | welcome.intakeChooseSource | presentation-only | — |
| lunaverse.ls-welcome | webview-message | welcome.intakeConfirm | excluded | — |
| lunaverse.ls-welcome | webview-message | welcome.importNovel | excluded | — |
| lunaverse.ls-welcome | webview-message | welcome.importProject | excluded | — |
| lunaverse.ls-welcome | webview-message | welcome.login | excluded | — |
| lunaverse.ls-welcome | webview-message | welcome.logout | excluded | — |
| lunaverse.ls-welcome | webview-message | welcome.newProjectInteractive | excluded | — |
| lunaverse.ls-welcome | webview-message | welcome.openAccount | presentation-only | — |
| lunaverse.ls-welcome | webview-message | welcome.openBook | excluded | — |
| lunaverse.ls-welcome | webview-message | welcome.openDemo | excluded | — |
| lunaverse.ls-welcome | webview-message | welcome.openVaultGraph | presentation-only | — |
| lunaverse.ls-welcome | webview-message | welcome.ready | presentation-only | — |
| lunaverse.ls-welcome | webview-message | welcome.refresh | presentation-only | — |
| lunaverse.ls-welcome | webview-message | welcome.registerWithInvite | excluded | — |
| lunaverse.ls-welcome | webview-message | welcome.resetSample | excluded | — |
| lunaverse.ls-welcome | webview-message | welcome.setLanguage | presentation-only | — |
| lunaverse.ls-workbench | manifest-command | lunaverse.compile | capability | script.compile |
| lunaverse.ls-workbench | manifest-command | lunaverse.generateAssetsMapping | capability | asset.mapping-update |
| lunaverse.ls-workbench | manifest-command | lunaverse.openRawText | covered-by-atom | file.open |
| lunaverse.ls-workbench | manifest-command | lunaverse.openStudio | presentation-only | — |
| lunaverse.ls-workbench | manifest-command | lunaverse.preview | capability | preview.prepare |
| lunaverse.ls-workbench | manifest-command | lunaverse.publish | presentation-only | — |
| lunaverse.ls-workbench | manifest-command | lunaverse.validate | capability | script.validate |
| lunaverse.ls-workbench | private-command | lunaverse.chrome.setStatus | presentation-only | — |
| lunaverse.ls-workbench | private-command | lunaverse.openScript | covered-by-atom | file.open |
| lunaverse.ls-workbench | private-command | lunaverse.syncChromeTab | presentation-only | — |
| lunaverse.ls-workshop | manifest-command | lunaverse.openReleaseCenter | presentation-only | — |
| lunaverse.ls-workshop | manifest-command | lunaverse.openWorkshop | presentation-only | — |
| lunaverse.ls-workshop | manifest-command | lunaverse.workshop.revealAsset | capability | file.reveal |
| lunaverse.ls-workshop | private-command | lunaverse.assets.productionConfig.get | capability | production.config-inspect |
| lunaverse.ls-workshop | private-command | lunaverse.assets.productionConfig.save | capability | production.config-update |
| lunaverse.ls-workshop | private-command | lunaverse.assets.refreshAgentRenderManifest | capability | manifest.refresh |
| lunaverse.ls-workshop | private-command | lunaverse.workshop.assetPreviewRead | capability | asset.preview-open |
| lunaverse.ls-workshop | private-command | lunaverse.workshop.ensureAssetSpecs | covered-by-atom | manifest.refresh |
| lunaverse.ls-workshop | private-command | lunaverse.workshop.generateMinigame | excluded | — |
| lunaverse.ls-workshop | private-command | lunaverse.workshop.productionDag | capability | production.dag-plan |
| lunaverse.ls-workshop | private-command | lunaverse.workshop.productionReviewOverride | excluded | — |
| lunaverse.ls-workshop | webview-message | workshop.addCustomStyleRef | capability | style.custom-reference-add |
| lunaverse.ls-workshop | webview-message | workshop.addReferenceImage | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.archiveStyleRow | capability | style.official-archive |
| lunaverse.ls-workshop | webview-message | workshop.cancelHeadlessRun | capability | production.run-cancel |
| lunaverse.ls-workshop | webview-message | workshop.cancelRun | covered-by-atom | production.run-cancel |
| lunaverse.ls-workshop | webview-message | workshop.clearBookCover | capability | book.cover-clear |
| lunaverse.ls-workshop | webview-message | workshop.clearUnusedAssets | capability | asset.clear-unused |
| lunaverse.ls-workshop | webview-message | workshop.copyAssetName | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.deleteCustomStyle | capability | style.custom-delete |
| lunaverse.ls-workshop | webview-message | workshop.editPlan | covered-by-atom | production.dag-plan |
| lunaverse.ls-workshop | webview-message | workshop.fetchStyleVersionBody | capability | style.version-inspect |
| lunaverse.ls-workshop | webview-message | workshop.fetchStyleVersions | capability | style.catalog-list |
| lunaverse.ls-workshop | webview-message | workshop.generateBookCover | excluded | — |
| lunaverse.ls-workshop | webview-message | workshop.generateStylePreview | capability | style.preview-generate |
| lunaverse.ls-workshop | webview-message | workshop.ingestNovel | capability | project.import |
| lunaverse.ls-workshop | webview-message | workshop.interruptSession | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.openAssetNative | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.openProductionArtifactNative | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.openLibrary | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.openLunaverseAgent | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.openLs | covered-by-atom | file.open |
| lunaverse.ls-workshop | webview-message | workshop.openLsLocation | covered-by-atom | file.reveal |
| lunaverse.ls-workshop | webview-message | workshop.openMinigameExternal | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.openNovelDialog | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.openPreview | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.openReleaseCenter | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.openVaultGraph | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.openVoiceCasting | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.pickBookCover | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.prefillLunaverseAgent | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.prepareVideoCgPrompt | covered-by-atom | asset.generation-readiness |
| lunaverse.ls-workshop | webview-message | workshop.publishNovelRelease | capability | release.publish |
| lunaverse.ls-workshop | webview-message | workshop.queryGenerationAdmission | capability | asset.generation-readiness |
| lunaverse.ls-workshop | webview-message | workshop.ready | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.refreshAllAssets | covered-by-atom | manifest.refresh |
| lunaverse.ls-workshop | webview-message | workshop.refreshBookProgress | covered-by-atom | asset.inventory |
| lunaverse.ls-workshop | webview-message | workshop.refreshCustomStyles | covered-by-atom | style.catalog-list |
| lunaverse.ls-workshop | webview-message | workshop.refreshStyleCatalog | covered-by-atom | style.catalog-list |
| lunaverse.ls-workshop | webview-message | workshop.regenAsset | covered-by-atom | asset.generate |
| lunaverse.ls-workshop | webview-message | workshop.regenEpisode | covered-by-atom | asset.generate |
| lunaverse.ls-workshop | webview-message | workshop.regenerate | capability | asset.generate |
| lunaverse.ls-workshop | webview-message | workshop.regenerateAssets | covered-by-atom | asset.generate |
| lunaverse.ls-workshop | webview-message | workshop.requestBookArtefacts | capability | asset.inventory |
| lunaverse.ls-workshop | webview-message | workshop.requestReadiness | covered-by-atom | asset.generation-readiness |
| lunaverse.ls-workshop | webview-message | workshop.retryBootstrap | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.requestReleaseScan | capability | release.scan |
| lunaverse.ls-workshop | webview-message | workshop.requestUnusedAssets | covered-by-atom | asset.inventory |
| lunaverse.ls-workshop | webview-message | workshop.restoreAssetVersion | capability | asset.restore |
| lunaverse.ls-workshop | webview-message | workshop.revealBookFolder | covered-by-atom | file.reveal |
| lunaverse.ls-workshop | webview-message | workshop.saveCustomStyle | capability | style.custom-save |
| lunaverse.ls-workshop | webview-message | workshop.saveGenerationModels | capability | generation.settings-update |
| lunaverse.ls-workshop | webview-message | workshop.setAssetReviewStatus | capability | asset.review-update |
| lunaverse.ls-workshop | webview-message | workshop.setOnboardingStatus | capability | project.onboarding-update |
| lunaverse.ls-workshop | webview-message | workshop.setSetting | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.signIn | excluded | — |
| lunaverse.ls-workshop | webview-message | workshop.styleChanged | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.syncStyleRow | capability | style.official-sync |
| lunaverse.ls-workshop | webview-message | workshop.updateAssetState | capability | asset.state-update |
| lunaverse.ls-workshop | webview-message | workshop.updateBookIdentity | capability | project.update |
| lunaverse.ls-workshop | webview-message | workshop.uploadStyleReference | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.voiceAudition | capability | voice.audition |
| lunaverse.ls-workshop | webview-message | workshop.voiceAuditionTextUpdate | covered-by-atom | voice.cast-update |
| lunaverse.ls-workshop | webview-message | workshop.voiceAutoAssignCatalog | capability | voice.catalog-auto-assign |
| lunaverse.ls-workshop | webview-message | workshop.voiceCastOpenManifest | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.voiceCastRefresh | capability | voice.casting-inspect |
| lunaverse.ls-workshop | webview-message | workshop.voiceCatalogSearch | capability | voice.catalog-search |
| lunaverse.ls-workshop | webview-message | workshop.voiceDescriptionUpdate | covered-by-atom | voice.cast-update |
| lunaverse.ls-workshop | webview-message | workshop.voiceSave | capability | voice.cast-update |
| lunaverse.ls-workshop | webview-message | workshop.voiceSyncBackend | capability | voice.sync |
| lunaverse.ls-workshop | webview-message | workshop.voiceUseCatalog | covered-by-atom | voice.cast-update |
| lunaverse.ls-preview | private-command | lunaverse.guidedTour.preparePreview | presentation-only | — |
| lunaverse.ls-preview | webview-message | lunaverse.guidedTour.action | presentation-only | — |
| lunaverse.ls-preview | webview-message | lunaverse.guidedTour.target | presentation-only | — |
| lunaverse.ls-studio | private-command | lunaverse.guidedTour.prepareStudio | presentation-only | — |
| lunaverse.ls-studio | webview-message | lunaverse.guidedTour.action | presentation-only | — |
| lunaverse.ls-studio | webview-message | lunaverse.guidedTour.target | presentation-only | — |
| lunaverse.ls-welcome | manifest-command | lunaverse.guidedTour.replay | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.activeBook.get | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.activeBook.select | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.creator.setAgentHandoffState | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.creator.refreshStepProgress | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.welcome.createFromAgent | excluded | — |
| lunaverse.ls-welcome | private-command | lunaverse.guidedTour.close | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.guidedTour.developerProof | excluded | — |
| lunaverse.ls-welcome | private-command | lunaverse.guidedTour.identifyWindow | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.guidedTour.lifecycle | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.guidedTour.prepareCreatorTarget | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.guidedTour.prepareLibrarySample | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.guidedTour.restoreAdapterState | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.guidedTour.resumeProductSession | presentation-only | — |
| lunaverse.ls-welcome | webview-message | lunaverse.creator.ready | presentation-only | — |
| lunaverse.ls-welcome | webview-message | lunaverse.guidedTour.action | presentation-only | — |
| lunaverse.ls-welcome | webview-message | lunaverse.guidedTour.target | presentation-only | — |
| lunaverse.ls-welcome | webview-message | welcome.deleteBook | excluded | — |
| lunaverse.ls-welcome | webview-message | welcome.importNovelCancel | presentation-only | — |
| lunaverse.ls-welcome | webview-message | welcome.importNovelConfirm | excluded | — |
| lunaverse.ls-welcome | private-command | lunaverse.guidedTour.shouldOffer | presentation-only | — |
| lunaverse.ls-workbench | private-command | lunaverse.agent.didChangeBook | presentation-only | — |
| lunaverse.ls-workbench | private-command | lunaverse.agent.verifyGatewaySkills | excluded | — |
| lunaverse.ls-workbench | private-command | lunaverse.agent.willChangeBook | presentation-only | — |
| lunaverse.ls-workbench | private-command | lunaverse.workbench.activeBookChanged | presentation-only | — |
| lunaverse.ls-workshop | manifest-command | lunaverse.workshop.recoverProductionDag | excluded | — |
| lunaverse.ls-workshop | private-command | lunaverse.guidedTour.prepareReleaseCenter | presentation-only | — |
| lunaverse.ls-workshop | private-command | lunaverse.guidedTour.prepareWorkshopAudio | presentation-only | — |
| lunaverse.ls-workshop | private-command | lunaverse.guidedTour.prepareWorkshopVisuals | presentation-only | — |
| lunaverse.ls-workshop | private-command | lunaverse.workshop.activeBookChanged | presentation-only | — |
| lunaverse.ls-workshop | webview-message | lunaverse.guidedTour.action | presentation-only | — |
| lunaverse.ls-workshop | webview-message | lunaverse.guidedTour.target | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.searchFashionLooks | capability | fashion.look-search |
| lunaverse.ls-welcome | private-command | lunaverse.agent.focusInput | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.agent.prefillInput | presentation-only | — |
| lunaverse.ls-workshop | private-command | lunaverse.agent.prefillInput | presentation-only | — |
| lunaverse.ls-welcome | manifest-command | lunaverse.creator.openVisualEditor | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.agent.addExternalEditorContext | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.agent.closeImportProject | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.agent.focusProject | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.agent.importSource | excluded | — |
| lunaverse.ls-welcome | private-command | lunaverse.agent.newProject | excluded | — |
| lunaverse.ls-welcome | private-command | lunaverse.agent.projects.list | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.agent.rememberEditorContext | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.chrome.expandAgentSidebar | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.creator.observeAgentSkill | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.creator.openFile | covered-by-atom | file.open |
| lunaverse.ls-welcome | private-command | lunaverse.welcome.beginImportDraft | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.welcome.closeImportDraft | presentation-only | — |
| lunaverse.ls-welcome | private-command | lunaverse.welcome.importDroppedGlobal | excluded | — |
| lunaverse.ls-welcome | private-command | lunaverse.welcome.importNovelGlobal | excluded | — |
| lunaverse.ls-welcome | private-command | lunaverse.welcome.importProjectGlobal | excluded | — |
| lunaverse.ls-welcome | private-command | lunaverse.welcome.importProjectWithAgent | excluded | — |
| lunaverse.ls-welcome | private-command | lunaverse.welcome.openStart | presentation-only | — |
| lunaverse.ls-welcome | webview-message | account.back | presentation-only | — |
| lunaverse.ls-welcome | webview-message | creatorDocument.agentContext | presentation-only | — |
| lunaverse.ls-welcome | webview-message | creatorDocument.openExternal | covered-by-atom | file.open |
| lunaverse.ls-welcome | webview-message | creatorDocument.openSource | covered-by-atom | file.open |
| lunaverse.ls-welcome | webview-message | creatorDocument.resolveConflict | excluded | — |
| lunaverse.ls-welcome | webview-message | library.importProject | excluded | — |
| lunaverse.ls-welcome | webview-message | library.logout | excluded | — |
| lunaverse.ls-welcome | webview-message | library.newProject | excluded | — |
| lunaverse.ls-welcome | webview-message | library.openAccount | presentation-only | — |
| lunaverse.ls-welcome | webview-message | library.openAssets | presentation-only | — |
| lunaverse.ls-welcome | webview-message | library.openFile | covered-by-atom | file.open |
| lunaverse.ls-welcome | webview-message | library.openOverview | presentation-only | — |
| lunaverse.ls-welcome | webview-message | library.openProjectFiles | presentation-only | — |
| lunaverse.ls-welcome | webview-message | library.pinProject | excluded | — |
| lunaverse.ls-welcome | webview-message | library.removeProject | excluded | — |
| lunaverse.ls-welcome | webview-message | library.reveal | covered-by-atom | file.reveal |
| lunaverse.ls-welcome | webview-message | library.selectStep | presentation-only | — |
| lunaverse.ls-welcome | webview-message | welcome.pickImport | excluded | — |
| lunaverse.ls-welcome | webview-message | welcome.prepareDroppedImport | excluded | — |
| lunaverse.ls-workshop | private-command | lunaverse.agent.addRememberedContext | presentation-only | — |
| lunaverse.ls-workshop | private-command | lunaverse.agent.bindProductionWorkflow | presentation-only | — |
| lunaverse.ls-workshop | private-command | lunaverse.agent.focusProductionWorkflow | presentation-only | — |
| lunaverse.ls-workshop | private-command | lunaverse.agent.rememberAssetContext | presentation-only | — |
| lunaverse.ls-workshop | private-command | lunaverse.agent.resumeApprovedMangaReview | excluded | — |
| lunaverse.ls-workshop | private-command | lunaverse.agent.reviewWorkflowArt | excluded | — |
| lunaverse.ls-workshop | private-command | lunaverse.agent.runWorkflowContentStep | excluded | — |
| lunaverse.ls-workshop | private-command | lunaverse.agent.updateProductionWorkflow | presentation-only | — |
| lunaverse.ls-workshop | private-command | lunaverse.agent.validateWorkflowReviewModel | excluded | — |
| lunaverse.ls-workshop | private-command | lunaverse.workshop.authorizeMangaApproval | excluded | — |
| lunaverse.ls-workshop | private-command | lunaverse.workshop.buildWorkflowMinigame | excluded | — |
| lunaverse.ls-workshop | private-command | lunaverse.workshop.contentWorkflow | excluded | — |
| lunaverse.ls-workshop | private-command | lunaverse.workshop.controlWorkflow | excluded | — |
| lunaverse.ls-workshop | private-command | lunaverse.workshop.generateWorkflowImage | excluded | — |
| lunaverse.ls-workshop | private-command | lunaverse.workshop.inspectProductionPlan | covered-by-atom | production.dag-plan |
| lunaverse.ls-workshop | private-command | lunaverse.workshop.publishWorkflowCover | excluded | — |
| lunaverse.ls-workshop | private-command | lunaverse.workshop.restoreProductionWorkflows | excluded | — |
| lunaverse.ls-workshop | private-command | lunaverse.workshop.settleMangaApproval | excluded | — |
| lunaverse.ls-workshop | private-command | lunaverse.workshop.workflowReview | excluded | — |
| lunaverse.ls-workshop | webview-message | workshop.addAgentAsset | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.applyBookCoverUpload | excluded | — |
| lunaverse.ls-workshop | webview-message | workshop.focusProductionWorkflow | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.generateMinigame | excluded | — |
| lunaverse.ls-workshop | webview-message | workshop.openLocalFile | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.openProductPage | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.previewMinigameTemplate | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.rememberAgentAsset | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.revealLocalFile | presentation-only | — |
| lunaverse.ls-workshop | webview-message | workshop.searchPortraitCasting | covered-by-atom | fashion.look-search |
| lunaverse.ls-workshop | webview-message | workshop.selectPortraitCasting | excluded | — |

## 7. 所有 GitHub workflow 入口

这里只核对已跟踪入口，不断言最近一次执行/环境秘密/线上发布状态。IDE Cloud retired 入口不可重新用于发版。

| 文件 | 名称 | 触发声明 |
|---|---|---|
| [.github/workflows/desktop-feed-promote.yml](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/.github/workflows/desktop-feed-promote.yml) | Desktop feed promote | {"workflow_dispatch": {"inputs": {"platform": {"description": "Desktop platform to promote independently", "required": true, "type": "choice", "options": ["macos", "windows"]}, "release_id": {"description": "Immutable release candidate ID (for example ide-1.3.6)", "required": true, "type": "string"}, "macos_source_commit": {"description": "Exact macOS beta source commit; required for macOS", "required": false, "type": "string"}, "macos_feed_sha256": {"description": "SHA-256 of exact validated macOS beta feed bytes", "required": false, "type": "string"}, "macos_artifact_sha256": {"description": "SHA-256 of the immutable macOS update ZIP", "required": false, "type": "string"}, "macos_artifact_size": {"description": "Byte size of the immutable macOS update ZIP", "required": false, "type": "string"}, "source_channel": {"description": "Candidate channel already published and tested", "required": true, "default": "beta", "type": "choice", "options": ["beta"]}, "target_channel": {"description": "Feed pointer to promote", "required": true, "default": "stable", "type": "choice", "options": ["stable"]}}}} |
| [.github/workflows/ide-cloud-ci.yml](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/.github/workflows/ide-cloud-ci.yml) | IDE Cloud CI (retired) | {"workflow_dispatch": {}} |
| [.github/workflows/ide-cloud-railway-release.yml](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/.github/workflows/ide-cloud-railway-release.yml) | IDE Cloud Railway Release (retired) | {"workflow_dispatch": {}} |
| [.github/workflows/ide-cloud-router-release.yml](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/.github/workflows/ide-cloud-router-release.yml) | IDE Cloud Router Release | {"workflow_dispatch": {"inputs": {"phase": {"description": "Release phase", "required": true, "type": "choice", "options": ["stage_canary", "promote", "rollback"]}, "source_commit": {"description": "Exact 40-character candidate commit on current main", "required": true, "type": "string"}, "release_id": {"description": "Human release-train identity", "required": true, "type": "string"}, "policy_version": {"description": "Unique sticky routing-policy version", "required": true, "type": "string"}, "candidate_manifest_json": {"description": "Immutable lunaverse.release-candidate.v1 JSON document", "required": true, "type": "string"}, "candidate_evidence_json": {"description": "Candidate-bound lunaverse.release-evidence.v1 JSON document", "required": true, "type": "string"}, "rollback_router_version": {"description": "Exact Cloudflare Worker version for rollback", "required": false, "type": "string"}}}} |
| [.github/workflows/ide-library-dns.yml](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/.github/workflows/ide-library-dns.yml) | Upsert Library DNS | {"workflow_dispatch": null} |
| [.github/workflows/ide-library-route.yml](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/.github/workflows/ide-library-route.yml) | Bind Library Router Route | {"workflow_dispatch": null} |
| [.github/workflows/ide-studio-cache-purge.yml](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/.github/workflows/ide-studio-cache-purge.yml) | Purge Studio Cloudflare cache | {"workflow_dispatch": null} |
| [.github/workflows/ide-studio-dns.yml](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/.github/workflows/ide-studio-dns.yml) | Upsert Studio DNS | {"workflow_dispatch": null} |
| [.github/workflows/lunascripts-authority.yml](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/.github/workflows/lunascripts-authority.yml) | Lunaverse Script authority | {"pull_request": null, "push": {"branches": ["main"]}} |
| [.github/workflows/macos-beta-withdraw.yml](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/.github/workflows/macos-beta-withdraw.yml) | macOS beta withdraw | {"workflow_dispatch": {"inputs": {"confirm": {"description": "Exact acknowledgement for a pre-stable beta withdrawal", "required": true, "type": "string"}, "candidate_version": {"description": "User-facing version that must currently own beta", "required": true, "type": "string"}, "candidate_commit": {"description": "Exact withdrawn desktop source commit", "required": true, "type": "string"}, "candidate_release_sequence": {"description": "Exact withdrawn monotonic release sequence", "required": true, "type": "string"}, "expected_beta_blob_sha": {"description": "GitHub blob SHA of beta/latest.json before withdrawal", "required": true, "type": "string"}, "expected_stable_version": {"description": "Stable version whose exact feed bytes restore beta", "required": true, "type": "string"}}}} |
| [.github/workflows/macos-release.yml](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/.github/workflows/macos-release.yml) | macOS release | {"workflow_dispatch": {"inputs": {"source_commit": {"description": "Exact 40-character candidate commit on current main", "required": true, "type": "string"}, "core_validation_mode": {"description": "Reuse, refresh, or bypass the VS Code core validation proof", "required": true, "default": "auto", "type": "choice", "options": ["auto", "refresh", "off"]}}}} |
| [.github/workflows/manga-modal-release.yml](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/.github/workflows/manga-modal-release.yml) | Manga CG Modal Release | {"workflow_dispatch": {"inputs": {"source_commit": {"description": "Exact 40-character candidate commit on current main", "required": true, "type": "string"}}}} |
| [.github/workflows/novel-library-dev-skill-mirror.yml](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/.github/workflows/novel-library-dev-skill-mirror.yml) | Mirror two Skills to Novel Library Dev | {"push": {"branches": ["main"], "paths": ["agents/adaptation/skills/novel-selector/**", "agents/adaptation/skills/novel-evaluator/**"]}, "workflow_dispatch": null} |
| [.github/workflows/repository-migration-guard.yml](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/.github/workflows/repository-migration-guard.yml) | Repository migration guard | {"pull_request": {"paths": [".github/workflows/**", "fork/release/**", "scripts/**", "tools/e2e/**", "package.json"]}, "push": {"branches": ["main"], "paths": [".github/workflows/**", "fork/release/**", "scripts/**", "tools/e2e/**", "package.json"]}, "workflow_dispatch": null} |
| [.github/workflows/skill-r2-production-publish.yml](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/.github/workflows/skill-r2-production-publish.yml) | Publish production Skills to R2 | {"push": {"branches": ["main"], "paths": ["agents/**/skills/**", "scripts/ide-skill-release.mjs", ".github/workflows/skill-r2-production-publish.yml"]}, "workflow_dispatch": null} |
| [.github/workflows/update-worker-release.yml](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/.github/workflows/update-worker-release.yml) | Update Worker Release | {"workflow_dispatch": {"inputs": {"phase": {"description": "Deploy candidate or roll back an exact version", "required": true, "type": "choice", "options": ["deploy", "rollback"]}, "source_commit": {"description": "Exact 40-character candidate commit on current main", "required": true, "type": "string"}, "expected_current_version_id": {"description": "CAS guard for the deployed Worker version", "required": true, "type": "string"}, "rollback_version_id": {"description": "Exact prior version; required only for rollback", "required": false, "type": "string"}}}} |
| [.github/workflows/windows-downloader-release.yml](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/.github/workflows/windows-downloader-release.yml) | Windows downloader release (OSS) | {"workflow_dispatch": null} |
| [.github/workflows/windows-installed-upgrade-gate.yml](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/.github/workflows/windows-installed-upgrade-gate.yml) | Windows installed upgrade gate | {"workflow_dispatch": {"inputs": {"candidate_revision": {"description": "Exact 40-character producer commit to install", "required": true, "type": "string"}, "producer_run_id": {"description": "Successful windows-release.yml run that produced the beta candidate", "required": true, "type": "string"}, "expected_stable_version": {"description": "Public User Setup version that must currently be live", "required": true, "default": "1.3.1", "type": "string"}, "expected_candidate_version": {"description": "Candidate version that must be encoded by the revision", "required": true, "default": "1.3.2", "type": "string"}}}} |
| [.github/workflows/windows-release.yml](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/.github/workflows/windows-release.yml) | Windows release (OSS) | {"workflow_dispatch": {"inputs": {"source_commit": {"description": "Exact 40-character candidate commit on current main", "required": true, "type": "string"}, "core_validation_mode": {"description": "Reuse, refresh, or bypass the VS Code core validation proof", "required": true, "default": "auto", "type": "choice", "options": ["auto", "refresh", "off"]}}}} |
| [.github/workflows/xcode-cloud-release.yml](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/.github/workflows/xcode-cloud-release.yml) | Xcode Cloud Release | {"workflow_dispatch": {"inputs": {"revision": {"description": "Exact 40-character lowercase origin/main release commit", "required": true, "type": "string"}}}} |

## 8. 结果与限制

[阅读主报告](../wiki/syntheses/lunaverse-ide-calibration-2026-09.md)。本附录不含生产 token、邀请码、数据库内容、用户本地设置、实际 trace 或未提交创作正文。

源仓只装了隔离依赖来运行本地检查，未变更 tracked 文件；原用户工作区保持未提交编辑。Wiki 当前内容经 MCP + revision 更新，历史 raw 不改。

复查时应重新固定 exact SHA，重新枚举基线页面/manifest/技能/注册表，逐项比较差异。列表数量只是防漏账本，不是创作质量、线上健康或安全认证。

## 9. 中间观察版本增量与补充验证（开发指南随后撤回）

第一次收尾 fetch 后完整阅读 `14c089322..49adbc3fb` 差异：只有六个开发协作文件改变，没有运行时、产品技能、配置、依赖、vendor 或发布 workflow 变化。该中间提交时间为 `2026-09-15T12:55:38+08:00`。下面三个开发指南链接只代表此刻历史，不是最终版本现行规范。

| 文件 | 处置 |
|---|---|
| `.codex/skills/lunaverse-git-delivery/SKILL.md` | 删除本机专用指导，不作为共享产品 Skill |
| `.codex/skills/lunaverse-worktree-create/SKILL.md` | 同上 |
| `.codex/skills/lunaverse-worktree-preview/SKILL.md` | 同上 |
| [docs/development/README.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/49adbc3fb31362195ed4b6e1a7111aaab3e89dff/docs/development/README.md) | 新增共享开发入口 |
| [docs/development/git-and-pull-request.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/49adbc3fb31362195ed4b6e1a7111aaab3e89dff/docs/development/git-and-pull-request.md) | 提交范围、中文描述、授权及发布结论边界 |
| [docs/development/worktree-build-test.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/49adbc3fb31362195ed4b6e1a7111aaab3e89dff/docs/development/worktree-build-test.md) | 跨平台隔离工作区、依赖复用、聚焦测试、UI host、隔离 Beta 与正式打包分开 |

曾补入规则、维护专题与校准报告，随后根据第 10 节的撤回同步移除执行建议；产品 `agents/**/skills/**` 的 33 个 Skill 没有因此改变。已撤回文档的 Beta 热补约定不可当作现行全员规范；本次没有运行任何热补、安装或发布。

在中间 SHA `49adbc3fb` 复跑八个聚焦 Node 文件：83 tests、73 pass、1 fail、9 skip，失败仍为两个能力清单缺项；guidance 仍有一个 AGENTS.md 断链。补充 Vitest 在 `14c089322` 与 `49adbc3fb` 均实际通过：book-layout 17、creator-step-progress 30、runtime-selector 6，合计 53。

Wiki 本地 pytest 43 passed；实际 MCP 结构检查零错误。初次完整性验证确认 32 个未改页字节一致、23 个历史正文和 24 个跨项目正文保留，原有 raw 未改，120 个不同初始源码路径存在，四组新知识查询可检索。有限凭据模式扫描零命中，不代表完整安全审计。Git 时间导致的提交前 stale 提示与最终远端 CI 在提交后另行核实，不预写成功。

完整追加证据：[补充验证记录](../raw/2026-09-15-lunaverse-ide-calibration-verification.md)。

## 10. 最终 main 442fd5369：开发指南撤回

第二次收尾同步观察到 `442fd53692d077c1e601231d79792c6c50b430e3`，时间 `2026-09-15T13:15:44+08:00`，提交为 `revert: 移除 PR #5 的开发规范文档 (#19)`。已通读完整差异：只删除第 9 节三份 `docs/development/` 文件，三份本机 Skill 没有恢复。

因此相对最初 `14c089322` 的净差异只有删除三个本机开发 Skill。120 个初始实现引用的 Git blob 与最终版本逐项相同；运行时、产品技能、依赖、配置、vendor 和测试输入没有变化。没有将第 9 节实跑结果改名为“在 442fd5369 重新运行”。

11 个现行页及目录已写明最终 SHA，规则与运维页仅保留开发指南撤回记录，不再推荐执行其中约定。历史 raw 不覆盖，新的观察追加为 [最终主线记录](../raw/2026-09-15-lunaverse-ide-calibration-final-main.md)。校准是明确版本快照，不是自动持续追踪 main 的订阅。
