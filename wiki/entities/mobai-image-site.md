---
title: mobai 生图网站（Midjourney via Legnext）
tags: [midjourney, legnext, feishu-login, railway, r2, image-generation]
sources: [raw/2026-09-09-mobai-image-site-design.md]
created: 2026-09-09
updated: 2026-09-09
---

mobai 团队内部的生图网站。成员用飞书登录，写提示词、选 Midjourney 模型、上传参考图，后台通过 Legnext 接口生图，参考图和结果长期保存在团队 R2，整套部署到独立 Railway 项目。本页记录产品范围、技术决策、代码结构和进度。需求原文是 2026-09-09 的飞书交接文档，仓库内副本在 `docs/product-design.md`。

## 一句话

一个替代“找管理员要 Midjourney 账号”的内部工具。实习生打开网站就能出图，管理员能看到每一次生成、每一次报错和每个人的额度。

## 项目位置与文档

| 项目 | 位置 |
|-|-|
| 代码目录 | `/Users/wangbo/Downloads/mob_midjourney`（2026-09-09 建库，尚未推远端） |
| 需求原文 | `docs/product-design.md`，来自飞书文档 https://xcn662409uz0.feishu.cn/docx/JueadAJnMoGZ0jxGYiTcwlbjncd |
| 设计说明 | `docs/superpowers/specs/2026-09-09-mobai-image-site-design.md` |
| 实施计划 | `docs/superpowers/plans/2026-09-09-plan-1-foundation.md`，后续计划按编号追加 |
| 供应商快照 | `docs/vendor/legnext/`（openapi.yaml、mj-image-params.v1.json、llms.txt，抓取日期 2026-09-09） |
| 前端参考包 | `docs/reference/novel-library/`，摘自 Novel Library 本地工作区（基础提交 b6ba857f，含未提交的浅色改动） |

## 产品范围

### 角色

| 角色 | 能做 | 不能做 |
|-|-|-|
| member | 生图，看自己的历史、参考图、失败原因、剩余额度，下载自己的结果 | 看别人的图、原始接口错误、密钥、管理员日志 |
| admin | 看团队全部生成与报错记录，筛选导出，调整额度，停用成员，暂停新任务 | 从页面读出 API 密钥，绕过后台额度检查 |

“项目所有者”是部署侧身份，通过一次性脚本 `scripts/bootstrap-admin.mjs` 指定初始管理员。脚本只匹配已经真实登录过的身份，绝不自动建用户，也不会让首位访问者自动成为管理员。

### 首版必须交付

飞书登录并限制 mobai 租户、模型切换、参考图上传与复用、文生图和带参考图生图、实时状态、结果存 R2、历史详情、下载、复用设置、生成与错误日志、成员额度、管理员停用。

### 首版有条件开放

放大、轻微变化、较大变化。只有所选模型和源任务支持、且账户联调完成后才显示可执行按钮。

### 明确不做

局部重绘、扩图、视频、团队共享图库、评论、批量生成、提示词 AI 助手、自动翻译、飞书外发通知、回调接收（首版靠 Worker 轮询）、深色主题。不引入 Tailwind、shadcn、Prisma。

### 页面

| 路径 | 内容 |
|-|-|
| `/login` | 文字标识、“使用飞书登录”、仅 mobai 成员可用说明。开发环境额外有开发登录表单 |
| `/create` | 提示词、参考图、模型、比例；下方按任务显示结果。登录后的默认页 |
| `/library` | 我的图片，搜索提示词，按模型和日期筛选 |
| `/jobs`、`/jobs/[id]` | 生成记录与任务详情 |
| `/me` | 头像姓名、个人额度、退出 |
| `/admin/jobs`、`/admin/errors`、`/admin/members` | 团队记录、问题记录、成员与额度，仅 admin |

## 技术决策

### 栈

Next.js 16.3.0 App Router、React 19.2.8、TypeScript 5 strict、CSS Modules、pnpm 10.30.3、pg 8.23 裸 SQL 加增量迁移、Node 22+ 的 `.mjs` Worker、Playwright 1.62。前端颜色、字体、控件、导航照搬 Novel Library 当前浅色实现，精确数值从 `src/app/globals.css` 读。

### 身份与会话

- 飞书授权码流程，后台生成一次性 state 并绑定发起登录的浏览器（Cookie `mobai_feishu_browser`），PKCE S256。
- 换到用户信息后核验 `tenant_key` 在 `LARK_LOGIN_TENANT_KEYS` 名单内。企业名称、邮箱后缀、前端传入的企业编号都不算数。
- 首次合法登录自动建工作区（按 tenant_key）和普通成员。外部身份唯一键 `(provider, app_id, tenant_key, open_id)`，保留 union_id，不按姓名或邮箱合并。
- 会话随机 token 只存 SHA-256 散列，Cookie `mobai_session` HttpOnly、Secure、SameSite=Lax。空闲 2 小时、绝对 12 小时。每次访问核对用户 `active`、工作区 `active` 和 `auth_version`。
- `src/proxy.ts`（Next 16 里 middleware 改名为 proxy）在每个请求前校验会话，非 GET 请求先检查同源，`/admin` 与 `/api/admin` 前缀要求 admin，然后写入 `x-mobai-user-id`、`x-mobai-user-role`、`x-mobai-user-name`、`x-mobai-workspace-id` 请求头，进入时先删除同名外来头。

### 本地开发的两个替身

只在 `APP_ENV=development` 生效，其他环境直接拒绝。

- 开发登录。`AUTH_DEV_LOGIN=true` 时开放 `POST /api/auth/dev-login`，用固定的 `dev-tenant` 和传入的姓名、角色建会话。生产构建里该路由返回 404。
- 本地对象存储。`STORAGE_MODE=local` 时 presign 返回同源 PUT 地址，文件写到 `OBJECT_STORAGE_LOCAL_DIR`。生产必须是 `r2`。（计划 2 实现）

### 模型与参数

`config/model-capabilities.json` 是页面和后台共用的唯一能力来源（计划 2 实现）。初始内容按 2026-09-09 抓取的 Legnext schema 整理。

| model_id | 供应商参数 | 初始 enabled | 参考能力 |
|-|-|-|-|
| mj-v7 | `--v 7` | true，默认 | 画面参考 iw、风格参考 sref/sw、人物或物体参考 oref/ow（费用 2 倍） |
| mj-v8.2 | `--v 8.2` | true | 画面参考、风格参考；不支持 oref/cref |
| mj-v8.1 | `--v 8.1` | false | 同 v8.2 |
| mj-v6.1、mj-v6 | `--v 6.1`、`--v 6` | false | 画面参考、风格参考、人物参考 cref/cw |
| mj-niji-6 | `--niji 6` | false | 同 v6，但不支持 `--no` |

enabled 只表示页面可选，`verified_at` 为空的模型标“未经本账户验证”。参数范围来源在配置文件的 `sources` 字段里标注。iw 0～3、stylize 0～1000、chaos 0～100、seed 0～4294967295 来自 Legnext schema；sw、ow 0～1000、cw 0～100 来自 Midjourney 官方文档，联调时核对。画面比例固定 `1:1`、`2:3`、`3:2`、`9:16`、`16:9`。

### 提示词编译

编译器同时给 Web 校验和 Worker 提交使用。输出形如 `<画面参考URL> <用户提示词> --v 7 --ar 2:3 --iw 1 --sref URL --sw 100 --oref URL --ow 100 --s 100 --c 0 --seed N --style raw`。Raw 一律编译为 `--style raw`。用户提示词里出现 `--v`、`--sref` 等受控参数时阻止提交，按 token 解析而非字符串替换。含签名 URL 的完整 text 只进受限请求快照，展示和日志用去掉查询串的版本。

### Legnext 接口要点（2026-09-09 核对）

- Base URL `https://api.legnext.ai/api`，鉴权头 `x-api-key`。`GET /v1/job/{job_id}` 不带鉴权，job_id 本身就是访问凭证，必须当秘密保存，不序列化给浏览器。
- `POST /v1/diffusion` 只收 `text`（1～8192 字）和可选 `callback`。模型和参数全部拼在 text 里。
- 状态 `pending / staged / processing / retry / completed / failed`。`staged` 是账户并发已满在排队，`retry` 是供应商在自动重发，本站不新建任务。
- 响应 `output.image_url`（四宫格）、`output.image_urls`（单张数组）、`output.seed`；`meta.usage` 有 `type`（credit 或 point）、`frozen`、`consume`。
- 重复 POST 会新建计费任务，没有幂等键，没有取消接口。超时不等于取消。
- 输出链接完成后保留 7 天；变化、放大等依赖源任务的动作在源任务创建后 3 天内可用。
- `POST /v1/variation` 与 `POST /v1/upscale` 都要 `jobId / imageNo(0～3) / type`。页面“第 1 张”对应 imageNo 0。
- v8 家族拒绝 `--cref --cw --oref --ow --sd --turbo`，v8.1 与 v8.2 另拒绝 `--q`。`--hd` 在 v8 家族费用 1.5 倍。
- 错误含义。400 参数或审核问题不可重试，401 密钥，402 额度不足，403 账户受限或审核拒绝，429 并发上限可稍后重试。
- callback 没有签名密钥，收到回调必须再查一次 Get Task 才能落库。

### 任务状态与幂等（计划 2 实现）

本站十个状态 `queued / submitting / submitted / processing / submission_unknown / persisting / storage_failed / completed / failed / cancelled / needs_attention`。站内创建任务用浏览器生成的 `operation_id`，数据库对 `(workspace_id, user_id, operation_id)` 建唯一约束。每次外部提交前写 `request_attempts`，进程在 submitting 状态退出且没有可靠 job_id 时进入 `submission_unknown`，不自动重发计费 POST。额度预留、任务写入和队列记录在同一事务完成，Worker 用 `FOR UPDATE SKIP LOCKED` 领取租约。

### 额度默认值

每人每天 20 项、每人同时 1 项、团队同时 2 项，统计日 `Asia/Shanghai`。缺少策略行时拒绝创建任务，不放开。

## 代码结构（计划 1 完成后）

```text
src/app/layout.tsx                 根布局，读身份头，渲染导航
src/app/login/page.tsx             登录页
src/app/(workspace)/{create,library,jobs,me,admin/*}/page.tsx
src/app/api/auth/feishu/{start,callback}/route.ts
src/app/api/auth/{logout,dev-login}/route.ts
src/app/api/{me,health/live,health/ready}/route.ts
src/proxy.ts                       会话校验与身份头
src/components/ui/                 Button、PageHeader、PageTabs、SegmentedControl（照搬参考包）
src/components/{AppNavigation,NavigationIcon,UserAvatar,LogoutButton}.tsx
src/lib/env.ts                     APP_ENV、APP_ORIGIN、开发登录开关
src/lib/db.ts                      pg 连接池
src/lib/api.ts                     jsonOk / jsonError / handleRoute，统一 request_id
src/lib/logging.ts                 脱敏日志
src/lib/auth/policy.ts             safeNextPath、sameOriginMutation、feishuNotice
src/lib/auth/session-store.ts      纯 SQL 的会话逻辑，测试直接跑
src/lib/auth/session.ts            cookie 包装
src/lib/auth/identity-store.ts     外部身份落库，飞书与开发登录共用
src/lib/auth/oauth-state-store.ts  state 单次消费
src/lib/auth/feishu.ts             授权码流程
src/lib/auth/dev-login.ts
src/lib/auth/bootstrap-admin.mjs
db/migrations/001_identity.sql     workspaces、users、external_identities、sessions、oauth_states、audit_events、error_logs
scripts/{migrate-database,bootstrap-admin,run-e2e}.mjs
tests/{unit,system,e2e}/           node:test 与 Playwright
```

### 测试策略

- 单元测试 `node --import tsx --test tests/unit/*.test.mjs`，纯函数与视觉规则（颜色变量、对比度、css 不写死颜色）。
- 系统测试 `tests/system`，每个用例新建独立临时库 `mobai_image_test_*`、跑迁移、跑完删库。
- 浏览器测试 `scripts/run-e2e.mjs`，建临时库、构建、随机端口启动、用开发登录进站，验收 320、390、768、1280、1440 五个宽度，截图存 `.runtime/screenshots/`。Playwright 用系统 Chrome（`channel: chrome`）。

## 分段与进度

| 计划 | 内容 | 状态 |
|-|-|-|
| 1 基础 | 骨架、设计系统、身份表、飞书登录、会话、proxy、开发登录、管理员初始化、页面壳子 | 2026-09-09 完成 |
| 2 生成链路 | 上传校验、存储抽象、模型配置、提示词编译、Legnext 适配器与 mock、Worker、生成 API、幂等恢复、额度账本 | 2026-09-09 完成。见下文“生成链路实现” |
| 3 页面 | 生图页（照 Midjourney 网页版 Create 页做）、我的图片、记录、详情、大图、手机布局 | 2026-09-09 完成，同日按用户要求重做成 Midjourney 深色界面。51 单元、22 系统、9 浏览器用例全过 |
| 4 管理与可靠性 | 错误日志、管理员三页、停用、导出 | 用户 2026-09-09 明确只要生成部分，管理员页面暂不做；错误日志、恢复保存、变化放大已随计划 2 实现 |
| 5 部署与联调 | Railway、真实飞书应用、R2 跨域、Legnext 小额联调、恢复演练 | 2026-09-09 已上线并接通飞书登录 https://mob-midjourney.up.railway.app （见“线上部署”一节）。本地真实联调通过。文生图 80 point、较大变化 120 point、带人物参考（oref）160 point 各一次。R2 用团队 moonshort 桶，前缀 `mobai-image/<env>`，公开域名 `https://r2-assets.mob-ai.cn`。浏览器直传经签名 PUT 到 R2，桶上加了 ID 为 `mobai-image-upload` 的 PUT 跨域规则（当前只放行 localhost:3100，正式域名部署时用 `scripts/r2-cors.mjs` 追加）。Railway 与飞书应用仍待做 |

## 界面（2026-09-09 改为照 Midjourney 网页版）

用户 2026-09-09 明确要求界面和交互与 Midjourney 网页版 Create 页一致，原先按交接文档做的 Novel Library 浅色风格作废。当天从 midjourney.com/imagine 读取计算样式重做，取值记在 `src/app/globals.css` 顶部。

- 深色为默认。页面底 `#0d0e12`，面板 `#1e1f25`，正文 `#ebecef`，弱文字 `#8f94a3`，边线 `#292c32`，强调橙 `#f2330d`，字体 DM Sans（随仓库打包，OFL 许可）。侧栏有“浅色模式”开关，写 cookie `mobai_theme`。
- 侧栏 214px，胶囊形导航项 160×38，当前项橙字橙底。底部是主题开关和用户胶囊。
- 输入区 54px 高、12px 圆角，左边“添加参考图”图标，右边“生成设置”图标，右侧另有一组“搜索我的提示词”。参考图以缩略图芯片显示在输入区上方，可切换画面、风格、人物三种用途并调强度。
- 设置面板是输入区下方的四宫格卡片。画面比例（预览框、竖幅/正方形/横幅、滑杆）、美学（风格化、怪异度、变化程度）、模型（标准/HD、版本、标准/Raw）、更多选项（草稿、种子、排除内容）。
- 结果流按日期分组。每个任务一行，左边四张图并排（6px 间距、6px 圆角），右边 383px 信息栏放提示词、参考图缩略图、参数芯片（照 MJ 写法 `ar 3:2`、`v 7`、`stylize 250`、`raw`）、悬停出现“重新运行 / 使用 / 复制”。图片悬停出现“轻微变化 / 较大变化 / 精细放大 / 创意放大”和下载。
- 大图查看器盖在主内容区上，侧栏和输入区照旧可用。右栏是“继续创作”动作表（变化、放大、重新运行、使用画面/风格/提示词/人物）和尺寸、模型、种子；最右一列是同组四张缩略图，左右方向键切换，Esc 关闭。
- 手机宽度单列，图片两列，底部四项导航。

## 生成链路实现（2026-09-09）

- 数据表在 `db/migrations/002_generation.sql`。assets、generation_tasks、task_references、task_outputs、task_events、request_attempts、job_queue、quota_policies、usage_ledger、worker_heartbeats。
- 上传走三步。`POST /api/uploads/presign` 登记素材并发上传地址（r2 为 S3 签名 PUT，local 为同源 `PUT /api/uploads/[id]/put?token=`），浏览器直传，`POST /api/uploads/[id]/complete` 入队 verify_asset。Worker 用 sharp 解码、按 EXIF 摆正并重新编码去掉元信息，写到正式 key，临时对象删除。只收 JPG、PNG、WebP，10MiB、4000 万像素以内。
- 生成走 `POST /api/generations`。`operation_id` 在 `(workspace, user, operation_id)` 上唯一，同内容重放返回原任务，不同内容 409。额度预留、任务写入、队列入队同一事务，`FOR UPDATE SKIP LOCKED` 领取。默认每人每天 20 项、每人同时 1 项、团队同时 2 项。
- Worker 是 `scripts/worker.mts`，`pnpm worker` 启动，`node --import tsx` 运行，和 Web 共用 `src/lib` 里的同一份实现。作业类型 verify_asset、submit_task、poll_task、persist_outputs。
- 提交前先写 request_attempts 再发请求。上游 4xx 归类失败并释放额度，401 和 402 同时把工作区 `generation_paused` 置真；429 延后再试；5xx、超时、响应丢失进入 submission_unknown，不再自动重发。重启后停在 submitting 超过两分钟的任务转 submission_unknown。
- 轮询按 5、10、15、20 秒加随机退避。completed 后逐张下载（只接受 HTTPS、白名单域名，Legnext 实际出图域名是 img.playjoy3d.com，2026-09-09 联调时补进名单、逐跳校验重定向、拒绝私网地址、60MB 上限），存原图与 512 缩略图，全部落盘后才 completed 并结算用量。部分失败进 storage_failed，可 `POST /api/generations/[id]/retry-storage` 只重试保存。
- 变化与放大走 `POST /api/generations/[id]/actions`，页面第 N 张对应 imageNo N-1，源任务创建后 72 小时内可用，放大结果不能再放大或变化。
- 模拟供应商 `src/lib/provider/mock.ts` 用 sharp 生成四张图，提示词里带 `[mock:400]`、`[mock:402]`、`[mock:429]`、`[mock:500]`、`[mock:timeout]`、`[mock:fail]`、`[mock:slow]`、`[mock:moderation]` 可触发对应场景。
- 生图页在 `src/components/generation/`。输入区、设置面板、参考图芯片、任务卡片、大图、图片库、记录表、详情。Enter 生成，Shift+Enter 换行，⌘/Ctrl+Enter 生成并保留提示词，支持拖拽和粘贴上传。设置存在 localStorage。

## 线上部署（2026-09-09）

- Railway 项目 `mob-midjourney`（id 0cfc6921-0d02-4031-9d7d-320a7d3afb98），环境 production，三个服务。`web` 跑网页，`worker` 跑后台任务，`Postgres` 数据库。两个服务同一仓库，靠 `SERVICE_ROLE` 环境变量在 `scripts/start.mjs` 里分叉。
- 域名 `https://mob-midjourney.up.railway.app`，Railway 提供的服务域名改名而来。R2 桶跨域规则已放行这个来源。
- 部署方式是本地 `railway up -s web -d` 与 `railway up -s worker -d` 上传当前目录，Railpack 自动识别 Next.js 加 pnpm，Node 22（`.node-version`）。没有连 GitHub 仓库，项目目前也没有远端。
- 迁移在 web 启动时执行（`MIGRATE_ON_START=true`），迁移脚本带咨询锁。`railway.json` 里的 preDeployCommand 实测没有生效，且 config-as-code 已被 Railway 标记为 2026-12-01 弃用。
- 线上环境是 `APP_ENV=staging`。飞书登录已接入（2026-09-09），口令入口已通过删除 `AUTH_DEV_LOGIN` 与 `AUTH_ACCESS_CODE` 关闭；需要临时口令登录时再加回这两个变量。
- 飞书应用是独立新建的“Mob Midjourney Online”，App ID `cli_aa2b333ee6f8dd06`，企业自建应用，能力只开了网页应用（主页 https://mob-midjourney.up.railway.app/create ），无额外权限，重定向 URL 为 `https://mob-midjourney.up.railway.app/api/auth/feishu/callback`。没有复用 Novel Library 的 Lunaverse Collector，两站互不影响。版本 1.0.0 先以所有者一人免审核发布验证登录，随后 1.0.1 把可用范围改为所有员工并发布（该账号发布免审核，2026-09-09 生效）。租户标识 `145796ecc30c575d`。
- 线上变量清单。APP_ENV、APP_ORIGIN、AUTH_DEV_LOGIN、AUTH_ACCESS_CODE、DATABASE_URL（引用 Postgres）、STORAGE_MODE=r2、R2_* 六项加 R2_PUBLIC_BASE、R2_PREFIX=mobai-image/staging、PROVIDER_MODE=legnext、LEGNEXT_API_BASE、LEGNEXT_API_KEY、GENERATION_ENABLED、UPLOAD_MAX_BYTES、REFERENCE_GET_TTL_SECONDS、NODE_ENV=production；web 另有 SERVICE_ROLE=web、DB_POOL_MAX、MIGRATE_ON_START；worker 另有 SERVICE_ROLE=worker、WORKER_CONCURRENCY、WORKER_DB_POOL_MAX。
- 默认额度 2026-09-09 放宽为每人每天 60 项、每人同时 3 项、团队同时 6 项（迁移 003）。
- 上线后用 Impasto Arcane 画风（`/Users/wangbo/Downloads/new style config/impasto-arcane`）的 casting 提示词加 male-face / female-face 风格参考（sw 700，V7，3:4，raw，stylize 250，chaos 4，未用 Omni）各跑一批，男女正脸各四张，各 80 point，风格与预览一致。

## R2 与联调记录（2026-09-09）

- 桶 `moonshort`，S3 端点 `https://<account>.r2.cloudflarestorage.com`，公开域名 `https://r2-assets.mob-ai.cn`。对象 key 是 `mobai-image/<env>/<reference|output|grid|thumbnail>/<workspace>/<asset>.<ext>`，临时上传放 `mobai-image/<env>/tmp/`，验证完删除。
- 参考图交给 Legnext 用公开域名地址（`R2_PUBLIC_BASE`），没有公开域名时退回 S3 签名 GET。联调证实 Legnext 能读公开域名，`--oref` 生效，结果里的狐狸和参考图一致。
- `pnpm r2:check` 写一个小对象、HEAD、签名链接匿名读、公开域名读、删除，四步全过才算配置可用。
- 浏览器直传靠桶的跨域规则。原有规则只放行 GET/HEAD，`scripts/r2-cors.mjs <origin...>` 会追加或替换一条 ID 为 `mobai-image-upload` 的 PUT 规则，只允许 content-type 与 content-length 头。
- 密钥只在本机 `.env.local`（gitignore）与将来的 Railway 变量里。Legnext key 与 R2 key 都曾贴在对话里，建议上线前在各自后台轮换。

## 待用户核对的外部事项

| 事项 | 为什么 |
|-|-|
| Legnext 密钥、余额、账户并发 | 2026-08-22 的冒烟记录只证明有过可用，不代表现在可用 |
| R2 bucket、凭证、签名链接能否被 Legnext 读取 | 决定参考图流程是否可行；不行就改用本站限定读取地址 |
| 飞书应用新建还是复用、mobai 的 tenant_key | 企业名称不能当授权依据 |
| 初始管理员的飞书身份 | 本人先登录一次，再跑 bootstrap 脚本 |
| Railway 项目、正式域名、测试环境 | 飞书回调和 R2 跨域需要准确域名 |
| 个人每日额度、可开放的模型 | 默认值不等于已批准预算 |

## 相关页面

- [[concepts/railway-production-deploy]] 是 lunaverse-backend 的 Railway 上线经验，本项目部署阶段可参考其 token 与迁移做法。
- Novel Library 的飞书登录实现是本项目身份模块的底本，差别在于去掉了旧账号认领和人工审核。

## Sources

- [设计说明](../../raw/2026-09-09-mobai-image-site-design.md)
