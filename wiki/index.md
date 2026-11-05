---
title: Wiki Index
updated: 2026-09-15
---

# Mob-Wiki Index

Welcome to the team knowledge base.

## Plan

- [[plan]] — 团队行动计划；本轮核对 IDE 关联边界，其余原日期记录

## Concepts

- [[concepts/agent-manuals-agents-md]] — Lunaverse IDE — 产品规则、领域 Skill 与作品上下文；固定主线校准 2026-09-15
- [[concepts/api-relay-verification-and-ecc-config-trim]] — 中转站满血验机方法 + ECC 全局配置瘦身 playbook
- [[concepts/asset-matting-hybrid]] — 历史｜Asset Matting Hybrid (A 默认 + 检测 + B 兜底)；非当前 IDE 执行指南
- [[concepts/asset-pipeline-aspect-ratio-recovery-2026-05]] — 历史｜Asset Pipeline Aspect-Ratio Recovery (NRBI 2026-05)；非当前 IDE 执行指南
- [[concepts/asset-pipeline-green-spill-fix-2026-05-09]] — 历史｜Asset Pipeline — Green-Spill Root Cause + RGB Unspill Fix (2026-05-09)；非当前 IDE 执行指南
- [[concepts/asset-pipeline-green-spill-runbook]] — 历史｜Asset Pipeline — Green-Spill Runbook (recipes for follow-up runs)；非当前 IDE 执行指南
- [[concepts/asset-pipeline-to-final-raw-cache-trap-2026-05-10]] — 历史｜to-final.py _raw Cache Trap；非当前 IDE 执行指南
- [[concepts/assetctl-integration-contract]] — assetctl — 当前原子能力与宿主执行边界；固定主线校准 2026-09-15
- [[concepts/assetctl-skills-sync-and-staging]] — 历史｜assetctl skills sync + Block 2/3 staging（codex skill 加载链路）；非当前 IDE 执行指南
- [[concepts/assets-produce-ide-workspace-contract]] — Lunaverse IDE — 规范书籍目录与本地素材映射；固定主线校准 2026-09-15
- [[concepts/backend-security-hardening-2026-06]] — lunaverse-backend 2026-06-10 四轨安全加固：Postgres 限流 + CORS allowlist + boot env 断言 + gitleaks gate + secret 轮换工具链（轮换执行待用户；生产 read-tier cheats 是硬依赖的决策记录）
- [[concepts/cg-pipeline]] — 历史｜CG Pipeline (07.5 step)；非当前 IDE 执行指南
- [[concepts/cli-gateway-protocol]] — CLI Gateway Protocol；本轮核对 IDE 关联边界，其余原日期记录
- [[concepts/codex-runtime-and-verification-layers]] — 历史｜codex 运行时（IDE 内） — auth 模型 + 验证层级；非当前 IDE 执行指南
- [[concepts/comfyui-modal-deploy]] — 历史｜ComfyUI on Modal — matting + upscale-image serverless deploy；非当前 IDE 执行指南
- [[concepts/db-connection-budget]] — lunaverse-backend DB 连接预算（2026-06-10）：运行时全走 Supavisor transaction 池 6543 + 每引擎代码内显式 connection_limit；session 池 5432 只给 CI migrate（15 client 硬上限，5-30 事故根因）；生产 probe 实测 50 client → 17 server conn
- [[concepts/dream-bonus-only-op]] — 2026-05-26 dream entry-patch 大改：3 个 v1 ops 全废、单一 `bonus_only` op（terminal placement + template Continue + LLM 写的 ✦DREAM 文案 + 机械路由）；feed 入口直接落 dream E1；no-mainline-mutation invariant（三层 defense：writer/reviewer/backend）
- [[concepts/dream-rec-component-1-tirt-estimator]] — dream-rec C1: Bayesian TIRT estimator. Laplace MAP + (user, story) testlet random effect + LLM-confidence-weighted ψ² uniqueness. Replaces the choice-count stub.
- [[concepts/dream-rec-component-2-llm-tagger]] — dream-rec Component 2 — LLM-as-annotator tagger
- [[concepts/dream-rec-component-3-genre-projection]] — dream-rec C3: per-genre projection matrix `M_g` (K_genre × K_global). Hybrid manual-seed + PCA refinement with shadow-swap versioning and identity-on-5-core cold-start fallback.
- [[concepts/dream-rec-component-4-dream-ranker]] — dream-rec C4: axis_match × engagement × freshness additive ranker with continuous sharpness blending. Resolves Component 0 O5; adds `used_cold_start_matrix` to /recommend response.
- [[concepts/dream-rec-component-5-cold-start]] — dream-rec C5: 5-item forced-choice onboarding questionnaire writing informative `(μ₀, Σ₀)` via the same TIRT likelihood. Independent `cold_start_response` table, no `ChoiceEvent` pollution.
- [[concepts/dream-rec-component-6-dashboard]] — dream-rec C6 (deferred): Streamlit dashboard for Loop A/C/B observability. Design locked, implementation awaits `recommend_log` + lunaverse funnel API.
- [[concepts/dream-rec-dev-runbook]] — dream-rec dev runbook
- [[concepts/dream-rec-integration-architecture]] — dream-rec integration architecture (Component 0)
- [[concepts/dream-rec-monorepo-migration]] — dream-rec 2026-05-24 monorepo migration: subtree merged into `cdotlock/lunaverse-backend → services/dream-rec/` with full history preserved; Dockerfile + dev compose + env keys landed; PR [#4](https://github.com/cdotlock/lunaverse-backend/pull/4) open, Railway service provisioning still pending ops.
- [[concepts/dream-rec-paper2-plan]] — Paper #2 方案（06-10 立项，06-11 阶段 A 完成）：表征隔离基准 spec v3.1（severity 网格 + RQ4 协议审计 + scope 预收缩）、查新 25+ 篇 GO（近邻 2512.13001/AlphaRec 已定位）、6 模型 embedding 集（~$9）、idea-evaluator 评审 Accept-with-Revisions 已防御、旧 matrix 实验 5 缺陷作废、IPM/TOIS 选刊、22 个科研 skill 装入 dream-recv2（含 K-Dense 统计三件套）、产品侧 10 条批评
- [[concepts/dream-rec-ranker-upgrade-2026-06]] — 排序器可选通道升级（2026-06-10）：Thompson 采样（冷启动第一屏人人不同，θ_cov 终于被用上）+ MMR/阻尼/UCB/协同融合，全部默认关字节一致；质量先验经实证驳回未搬；分支 `feat/dream-rec-recsys-upgrade` 本地待 push；协同通道等 v2 affinity 端点。
- [[concepts/dream-rec-trigger-v2-coexistence]] — Scope split between dream-rec (content-ranking) and dream trigger v2 (dream-timing): asset-by-asset decision matrix, three integration commits in `/tmp/msb-dream-rec` (not pushed), deferred items (event weight surface, cross-service vector read).
- [[concepts/dream-trigger-v2-mechanical]] — Producer-side dream trigger v2 (2026-05-21): pure-mechanical evaluator (no LLM) — UserNovelProfile vector + weighted running mean + cosine drift + sharpness gates, replaces v1 single-gate. Drops phase dedup; first-dream保送 keeps committed_success ≥ 3.
- [[concepts/dreaming-universe]] — 玩家画像触发的共享 Dream 支线宇宙：Episode graph + assignment-gated overlay + Python dream-agent
- [[concepts/elvina-3d-character-pipeline]] — 可动 3D 角色制作流程（Elvina 实践 2026-10，Tripo + Blender）：T 字站姿分部件生成身体、Blender 手搭骨架；每种表情单独生成头（55+20 积分），成片只留一颗头，嘴部颜色+法线+形态键烘到主头上用「表情」滑杆切换；坑、成本、遗留问题、文件位置
- [[concepts/episode-writer-music-strategy]] — 历史｜Episode Writer · BGM 策略 & music-normalizer 流程；非当前 IDE 执行指南
- [[concepts/find-your-li-content-redesign-2026-08]] — Find Your LI（RC 匹配测试引流页）2026-08 内容改版：审查结论（模板填空结果页 / 题库信号失衡 stability+ 24:6 / 78% 用户同卡 / 中文混入 bug）+ v1.0 方案（15 题重制、100 角色专属内容库、稀有度蒙特卡洛、roast line、questions.json 单源 + vm 加载防双实现漂移）
- [[concepts/four-layer-philosophy]] — SKILL / CLI / MCP / API Four-Layer Philosophy；本轮核对 IDE 关联边界，其余原日期记录
- [[concepts/frame-interpolation-spec]] — 历史｜角色表情插帧实施方案；非当前 IDE 执行指南
- [[concepts/gateway-bypass-and-origin-lockdown-2026-06-10]] — Gateway-bypass + Origin lockdown cutover 2026-06-10
- [[concepts/iap-sku-pricing]] — IAP 6 档 SKU 官方定价（$1.99–$99.99）+ 首充赠送比例（+100%–+200%），唯一定价真相源
- [[concepts/ide-invite-codes-single-use]] — 历史｜IDE Single-Use Invite Codes；非当前 IDE 执行指南
- [[concepts/ide-tool-gateway-concurrency-limit]] — 历史｜IDE Tool Gateway Concurrency Limits；非当前 IDE 执行指南
- [[concepts/ls-format]] — 历史｜Lunascripts (LS) 格式规范；非当前 IDE 执行指南
- [[concepts/ls-spec-redesign-2026-06]] — 历史｜LS Spec Redesign (2026-06-04)；非当前 IDE 执行指南
- [[concepts/lunaria-web-agent-v2]] — Lunaria Web — Agent v2（渐进式技能加载 + 双模式 + 持久化大纲 + AI 写提示词）；本轮核对 IDE 关联边界，其余原日期记录
- [[concepts/lunaverse-ide-ai-integration]] — Lunaverse IDE — Pi 运行时、统一登录与 AI 交互；固定主线校准 2026-09-15
- [[concepts/lunaverse-ide-creator-progress]] — Lunaverse IDE — 作品步骤、审查与完成状态；固定主线校准 2026-09-15
- [[concepts/lunaverse-ide-ls-contract]] — Lunaverse IDE — LS 4.0 消费契约与历史兼容；固定主线校准 2026-09-15
- [[concepts/lunaverse-ide-release-and-operations]] — Lunaverse IDE — 发布、工程恢复与维护验收；固定主线校准 2026-09-15
- [[concepts/lunaverse-ide-skills-and-production]] — Lunaverse IDE — 技能分发与生产执行；固定主线校准 2026-09-15
- [[concepts/lunaverse-new-user-dropoff-tracking]] — Lunaverse 新用户流失埋点需求 v0.1（2026-11-02，待评审）：开场视频通过率 22%，同步 tutor 性别年龄到分析库，新增 6 个视频和退出事件，按阶段拆性别年龄
- [[concepts/lunaverse-tasks-and-apple-email]] — Lunaverse 任务列表与 Apple 绑定邮箱（2026-11-04，待评审）：按 Key Message 拆任务、我的页入口、邮件订阅和新邀请规则
- [[concepts/lunaverse-tutor-v1-1]] — Lunaverse Tutor v1.1 版本优化（2026-11-04，待评审）：删邮箱和游客登录、tutor 奖励和 Discord 弹窗、Moons 100 倍方案（待确认）、通用领取埋点
- [[concepts/matting-v10-sharpen-alpha-bug-2026-05-28]] — 历史｜V10 抠图遗漏 sharpen_alpha bug + 修复（2026-05-28）；非当前 IDE 执行指南
- [[concepts/moonshort-ide-uiux-audit-2026-06]] — 历史｜Moonshort IDE UI/UX Audit + Fix Log (2026-06)；非当前 IDE 执行指南
- [[concepts/mp-cross-signal-author-guidance]] — MP Cross-Signal Author Guidance；本轮核对 IDE 关联边界，其余原日期记录
- [[concepts/novel-dream-artifact]] — `NovelDreamArtifact` 1:1 sidecar of Novel holds `characterArcs` (renamed from `characterBible`) + `assetMapping` + audit meta; 2026-05-24 抽表 to separate admin authoritative data from dream-pipeline regenerable derived data
- [[concepts/novel-game-config]] — 每部剧本可配置的属性系统（SAN-slot + 4 检定变量 + 平台级数值整理）
- [[concepts/otome-script-quality-evaluator]] — 乙女逐剧本质量评分器设计（per-script quality gate）；本轮核对 IDE 关联边界，其余原日期记录
- [[concepts/otome-writing-benchmark-survey-2026-06]] — 乙女小说写作 Benchmark 调研 + 自建指标草案（2026-06-04）；本轮核对 IDE 关联边界，其余原日期记录
- [[concepts/production-pipeline-two-phase]] — 历史｜Production Pipeline — Two-Phase IDE Submit + Admin Activate；非当前 IDE 执行指南
- [[concepts/railway-production-deploy]] — lunaverse-backend 怎么上 Railway 生产：service 拓扑 + `railway-production-deploy.yml` workflow（confirm/skip_migrations/force_* inputs）+ account-token 鉴权（2026-06-05 CLI 回归 `railway up` 拒 project token 的复盘）+ 为何 skip in-CI migrate（prod 无 `_prisma_migrations` → P3005 + pooler 15-client 上限）+ **additive-only 零删库 cutover playbook**（merged-schema `migrate diff` 证 prod 已是 HEAD 超集 → 不 apply drop）+ 单本免 redeploy re-seed + TTS warmth 行级寻址；2026-06-06 LS realignment 上线为 worked example
- [[concepts/remix-anywhere]] — 玩家长按对白 → D20+DC → LLM 生成 InsertPatch 插入剧情；Drama Remix 2026-05-05 整体摘除；forward planner 2026-05-24 改单 plan + 2-stage pick→write 跨非 dream 全分支
- [[concepts/second-chorus-asset-pipeline]] — 历史｜Second-Chorus 素材流水线（自包含 / 云端可跑 / 可复用模板）；非当前 IDE 执行指南
- [[concepts/server-layer]] — mobai-agent Server Layer；本轮核对 IDE 关联边界，其余原日期记录
- [[concepts/sfx-pipeline]] — 历史｜SFX Pipeline Design；非当前 IDE 执行指南
- [[concepts/signal-int-backend]] — Backend Support for LS `@signal int`；本轮核对 IDE 关联边界，其余原日期记录
- [[concepts/stable-step-id]] — Stable Step ID & Content-Addressed Cursor；本轮核对 IDE 关联边界，其余原日期记录
- [[concepts/style-langfuse-migration]] — Lunaverse IDE — 当前风格包与历史 Langfuse 迁移；固定主线校准 2026-09-15
- [[concepts/supabase-backend-bootstrap]] — 2026-05-29 backend 生产 Postgres 切 Supabase；fresh-bootstrap 流程（`db push + raw contract SQL`，`BOOTSTRAP_TARGET_DB_NAME` 防呆）；migration 是增量补丁、不支持空库 replay（2026-04-27 option A 决策）；灾备走 DB 备份 / restore
- [[concepts/unfolded-visual-novel]] — Unfolded 风格互动视觉小说展示形态与素材管线
- [[concepts/villain-season-demo]] — 恶人季 Heart Signal NA otome 短剧 demo（3 EP + 1 dream，双语 EN+ZH 平行 novel，autoAssign + bonus_only 强制 dream，22 SFX/7 角色/6 BG/3 BGM，英文 Breeze 配音 + TTS 上 R2，机制全覆盖含 Remix/Dream/signal/affection/butterfly/achievement/trick/minigame）
- [[concepts/ad-creative-pipeline-first-round-2026-11]] — 按作品投放素材制作流程（第一轮测试，2026-11-03）：MJ 定人物图、Ideogram 姿势稿出帧、H3 视频、本地真实界面渲染、官方 outro；含测试报告和问题清单链接
- [[concepts/poster-reference-matching-tagging]] — 海报参考匹配与打标 v0.2 已实施（2026-09-09）：4538 张 IMP 海报用网关 grok-4.6 打匹配标签（人数/年龄/时代/场景/情绪封闭，物件与主体开放），jina 向量比对，grok 终选 5 张；全量 10.19 美元 3 小时；Cornered Beast 与 Heir 两部样本简报已出；豆包与 Claude API 弃用；飞书 UVSDdJCwJoxy0WxiupFch914neb

## Entities

- [[entities/agent-forge]] — Agent-Forge；本轮核对 IDE 关联边界，其余原日期记录
- [[entities/assets-produce]] — Assets-Produce；本轮核对 IDE 关联边界，其余原日期记录
- [[entities/cli-gateway]] — CLI Gateway；本轮核对 IDE 关联边界，其余原日期记录
- [[entities/dramatizer]] — Dramatizer；本轮核对 IDE 关联边界，其余原日期记录
- [[entities/dramatizer-ls]] — Dramatizer-LS；本轮核对 IDE 关联边界，其余原日期记录
- [[entities/lunaria-web]] — lunaria-web；本轮核对 IDE 关联边界，其余原日期记录
- [[entities/lunascripts]] — Lunascripts (LS) Interpreter；本轮核对 IDE 关联边界，其余原日期记录
- [[entities/lunaverse-backend]] — Lunaverse Backend；本轮核对 IDE 关联边界，其余原日期记录
- [[entities/lunaverse-client]] — Lunaverse Client；本轮核对 IDE 关联边界，其余原日期记录
- [[entities/lunaverse-ide]] — Lunaverse IDE；固定主线校准 2026-09-15
- [[entities/lunaverse-poster-ref]] — Book Cover Match 海报参考站（2026-09-09 立项，Railway 线上服务）：飞书登录、导出包抽画像或 API 直提 profile、4538 张海报三版本各选 5 张；API token 接入与自动部署 2026-09-21；含页面、数据表、流水线、费用实测
- [[entities/mob-ai-router]] — Mob AI Router；本轮核对 IDE 关联边界，其余原日期记录
- [[entities/mob-mini-agent]] — Pi-based company Agent foundation with Moonshot runtime practices, observability, and compaction safety
- [[entities/mob-sandbox-ops]] — Self-hosted Daytona/OpenHands/Claude Code sandbox platform and operator runbook
- [[entities/mobai-agent]] — mobai-agent；本轮核对 IDE 关联边界，其余原日期记录
- [[entities/mobai-image-site]] — mobai 生图网站（Midjourney via Legnext，2026-09-09 立项）：飞书登录限 mobai 租户、参考图存 R2、Legnext 提交与幂等保护、Railway 独立部署；生成链路与生图页已完成，待接真实密钥联调
- [[entities/vibe-motion]] — AI-driven Remotion motion graphics workspace producing Lunaverse promo videos (9:16 portrait); lunaverse-intro delivered, lunaverse-app-promo iterating (72s + 30s/15s cuts, villain-season real assets, Breeze TTS)
- [[entities/video-agent-claude-wangbo]] — Claude Code video shot prompt workflow with Seedance gateway, OSS validation, and ablation-backed skill package

## Syntheses

- [[syntheses/cloud-deployment-architecture]] — Cloud Deployment Architecture；本轮核对 IDE 关联边界，其余原日期记录
- [[syntheses/data-silence-failure-class]] — 历史｜Data-Silence 失败类（VN Pipeline · 作者漏 render 家族）；非当前 IDE 执行指南
- [[syntheses/lunaverse-meta-day1-retrospective-2026-10]] — Lunaverse Meta 首日投放复盘（美东 2026-10-28）：CPI 4.77、开场空白流失四成、0 付费、数据口径与下一轮动作
- [[syntheses/lunaverse-ide-calibration-2026-09]] — Lunaverse IDE Wiki 整体校准 — 2026-09-15；固定主线校准 2026-09-15；最终对齐 main 442fd5369，含补充验证与开发指南撤回记录。
- [[syntheses/lunaverse-rename-migration]] — 历史｜2026-06 Lunaverse 改名方案（映射失真，禁止直接执行）；非当前 IDE 执行指南
- [[syntheses/platform-onboarding-guide]] — MobAI 平台全景指南；本轮核对 IDE 关联边界，其余原日期记录
- [[syntheses/product-strategy-decisions]] — 产品战略决策记录；本轮核对 IDE 关联边界，其余原日期记录
- [[syntheses/render-time-silent-drop-failure-class]] — 历史｜Render-Time Silent Drop 失败类（VN Pipeline v4.1-v4.11 同构族）；非当前 IDE 执行指南

## Tools

- [[tools/publish-report]] — publish-report CLI
