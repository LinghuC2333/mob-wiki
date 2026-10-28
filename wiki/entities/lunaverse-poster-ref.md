---
title: lunaverse-poster-ref（Book Cover Match）
tags:
- nextjs
- postgres
- railway
- poster
- api-token
- feishu-login
- internal-tool
sources:
- raw/2026-09-09-poster-ref-site-design.md
- raw/2026-09-13-book-cover-match-handoff.md
- raw/2026-09-21-book-cover-match-handoff.md
created: '2026-09-09'
updated: '2026-10-27'
---

# lunaverse-poster-ref（Book Cover Match）

团队内部网站：成员用飞书登录，选一个 Lunaverse IDE 导出文件夹，网站从允许清单文件里抽作品画像，在 4538 张 IMP Awards 海报中匹配构图参考图，按全员（full）、主 CP（couple）、单人（solo）三个版本各挑 5 张并附理由和原图链接。

- 仓库：`LinghuC2333/lunaverse-poster-ref`（私有），本机代码目录 `/Users/wangbo/Downloads/lunaverse-poster-ref`
- 线上：https://book-cover-match.up.railway.app
- Railway 项目：`lunaverse-poster-ref`（Kaito Wang's Projects，production），含 `web` + `Postgres` 两个服务
- 本地端口：`http://127.0.0.1:3110`，本地库 `lunaverse_poster_ref`
- 匹配算法来自 [[concepts/poster-reference-matching-tagging]]，立项设计原文见 [source](../../raw/2026-09-09-poster-ref-site-design.md)

## 部署

`railway.json` 使用 RAILPACK：`pnpm build` 构建、`pnpm start` 启动（先跑 `db:migrate` 和 `seed-posters`，库已灌满时跳过）。健康检查 `/api/health/ready`，超时 120s，失败重启上限 10 次。

**2026-09-21 发现并修复**：web 服务此前没有绑定 GitHub 源（push 不触发部署，交接文档里"部署绑定哪个提交待核对"的根因）。已通过 `railway service source connect --repo LinghuC2333/lunaverse-poster-ref --branch main --service web` 绑定，现在 push 到 main 会自动构建部署（首个自动部署 c9cdf827 已 SUCCESS）。

## 认证模型（双轨）

| 方式 | 凭据 | 适用 |
|---|---|---|
| 飞书会话 | `poster_session` cookie（空闲 2h / 绝对 12h） | 浏览器页面 + 网页操作 |
| API token | `Authorization: Bearer bcm_…` | 脚本、CLI、IDE 集成 |

- Bearer 请求在 `src/proxy.ts` 跳过 `sameOriginMutation` 同源检查（token 非 ambient credential，无 CSRF 风险）；无效 token 对 `/api/*` 返回 401 JSON。
- `requireUser()` 两种都认；`requireSessionUser()` 只认 cookie——**token 管理接口只走 session**，泄漏的 token 不能铸造新 token。
- token 表 `api_tokens`（迁移 `003_api_tokens.sql`）：只存 sha256 和展示前缀，`expires_at` 为 NULL 即永久，`revoked_at` 即时生效，`last_used_at` 按 1 分钟粒度更新。每人最多 20 个有效 token。

### Turbopack DCE 事故记录（2026-09-21）

开发模式 Turbopack 曾把 `requireSessionUser` 里 `if (!user) throw` 判空优化成 `"TURBOPACK compile-time falsy"` 恒假并删除，导致 Bearer 请求拿到 null user 走到 `user.id` 抛 TypeError（500 而非 401/403）。改为内联查询 + `user === null || user === undefined` 显式判空后，dev 与 `next build` 产物都验证正确。**今后在 auth 关键路径新增"判空 throw"后，建议 grep 构建产物确认分支存活**（搜中文错误串即可）。

### 飞书登录

飞书授权码流程加 PKCE，state 单次消费并绑定发起浏览器，换到用户信息后核验 tenant_key 在 `LARK_LOGIN_TENANT_KEYS` 内，首次登录自动建 member。会话 token 只存散列，Cookie HttpOnly、Secure、SameSite=Lax。飞书应用用独立的 Book Cover Match（App ID `cli_aa2b2c2afcb8dcc4`，可用范围全员，1.0.0 免审发布），不和 Novel Library、生图站共用。

管理员可用 `scripts/bootstrap-admin.mjs feishu:<tenant_key>:<open_id>` 指定，线上另配了 `ADMIN_BOOTSTRAP_NAME=王博`，登录后自动提升。member 只能看自己的 run，admin 能看全部。开发环境 `APP_ENV=development` 且 `AUTH_DEV_LOGIN=true` 时开放 `POST /api/auth/dev-login`，生产构建返回 404。

## API 面

完整文档在仓库 `docs/api.md`。一览：

| 接口 | 说明 |
|---|---|
| `GET /api/me` | 验证 token / 当前用户 |
| `GET /api/profile-schema` | profile 字段说明、枚举值、归一化规则与示例（自描述） |
| `POST /api/runs` | 二选一：`{profile}` 直提 WorkProfile（推荐，跳过画像模型，从 embedding 起步）；或 `{work_title, files:[{path,text}]}` 走服务端抽画像。profile 走宽容归一化：能修就修并在 `warnings` 里说明，只有无 cast / motifs+subject 皆空 / 非对象才 400。`size`/`sha256` 可选（服务端计算并校验）；路径可带顶层目录前缀；非允许清单文件跳过并在响应报 `skipped` |
| `GET /api/runs?q=` | 列自己的记录 |
| `GET /api/runs/{id}` | 状态、画像、结果、用量 |
| `POST /api/runs/{id}/rerun` | 提交修改后 `profile` 派生新任务 |
| `GET /api/runs/{id}/brief` | 下载 Markdown 简报（未完成 409） |
| `GET/POST /api/tokens`、`DELETE /api/tokens/{id}` | token 管理（仅网页会话） |

响应包装：成功 `{data, request_id}`，失败 `{error:{message}, request_id}`。

## 页面

| 路径 | 内容 |
|---|---|
| `/login` | 「使用飞书登录」，仅 mobai 租户。开发环境多一个开发登录表单 |
| `/` | 选文件夹。选中后本地列出将上传的文件和跳过的数量，点「开始匹配」。下方是我最近的匹配 |
| `/runs/[id]` | 先作品画像，再三个人数版本的海报清单。画像区可编辑并「按修改重跑」。每个版本先终选 5 张（缩略图、标题年份、分数、理由、原图链接），再折叠全部候选和分项分。右上角「下载简报」 |
| `/runs` | 我的全部匹配，按时间倒序，状态、作品名、费用 |
| `/admin/runs` | 全团队匹配记录，按人、作品、日期筛，仅 admin |
| `/me` | 头像姓名、今日剩余次数、退出 |

结果页在任务未完成时每 2 秒轮询一次状态，依次显示抽画像、算向量、匹配、终选四个阶段。

## 上传契约（2026-09-21 放宽）

原设计是浏览器 `webkitdirectory` 选文件夹、客户端算 sha256/size、且要求提交文件全部命中允许清单。放宽后服务端自己 `findRoot` 定位导出层、自算 sha256/size、跳过非清单文件——API 调用方可以把整个导出目录文本直接塞进来。限制：≤500 文件、请求合计 ≤12MB、清单内合计 ≤4MB、必须含 `01-novel-evaluator/evaluation-report.md`。允许清单见 `src/lib/poster/allowlist.ts`。

同日进一步开放 `profile` 直提：API 调用方提交符合 `WorkProfile` schema（`src/lib/poster/profile-schema.ts`，词表与海报标签同源）的结构化画像，**宽容归一化**（`normalizeProfile`：枚举非法值就近兜底、中文 motif 丢弃、无 mc 提第一个、多 mc 保留第一个，全部写进响应 `warnings`）后写入 `profile_auto`/`profile_final`，runner 从 `embedding` 起步——**不调画像模型**，单任务只剩 embed+pick 成本（实测 $0.03，约为文件路径的五分之一）。只有三种情况 400：非对象、cast 空、motifs+subject 皆空。`profile` 与 `files` 互斥，同传 400。浏览器文件夹上传路径不变。`GET /api/runs/{id}` 返回的 results 里 candidates/picks 已注入 `thumb_url`/`image_url`（picks 另带 `path`/`title`），调用方拿到即可直接取图；消费约定是 **picks（grok 看图挑的 5 张）优先，candidates（规则分 top24）兜底**。

### 允许清单（立项时的版本）

浏览器只读取并上传下面这些相对路径，图片、novel.txt 和 03 之后的目录不离开成员电脑。立项时单文件上限 1 MB、合计 4 MB，导出包顶层多一层作品目录时，按包含 01-novel-evaluator 的那一层定位根目录。

| 相对路径 | 用途 |
|---|---|
| `book.json` | 作品标题 |
| `01-novel-evaluator/evaluation-report.md` | 题材标签、世界观、判定、主线 |
| `01-novel-evaluator/relationship-map.md` | 人物关系与 LI 候选 |
| `01-novel-evaluator/storyline.md` | 关键场景，截取前 20 KB |
| `02-character-architect/li-selection-decision.md` | 正式 LI 名单 |
| `02-character-architect/mc-bible*.md` | MC 性别、年龄、外貌、关键道具，每份截取前 12 KB |
| `02-character-architect/li-bible*.md` | LI 性别、年龄、外貌，每份截取前 12 KB |

现行清单以仓库 `src/lib/poster/allowlist.ts` 为准。

## 流水线

1. 抽画像（只有 files 路径走这步）。允许清单正文按固定顺序拼接，交给网关按 JSON schema 输出画像，字段与海报标签同一套，包括 genre、mood 主次、era、setting、motifs 开放词 6 到 14 个、subject 一句、cast（MC 与每个 LI 的姓名、性别、年龄段）、inference_notes。有 02 目录以 li-selection-decision 为准，没有就从评估报告推断并写进 inference_notes。
2. 变体。从 cast 派生 full、couple、solo 三个人数版本，full 与 couple 相同时只跑一次。
3. 向量。motifs 加 subject 用 `jina-embeddings-v5-text-small` 算查询向量。
4. 匹配。硬过滤人数相等且题材重合，不足 8 张依次放宽到人数 ±1、放弃题材。软分 mood 30（forbidden 视同 sensual、dark、romantic 得 22）、era 15、setting 10、age 10、性别配比 5、motifs 20（余弦按全池第 10 到第 99 百分位映射），取前 24。
5. 终选。前 16 张的缩略图连标签交给 `PICK_MODEL` 挑 5 张，各写两句中文理由。缩略图由服务器带自定义 UA 从 R2 站点取，只在内存里过一下，本站不存图。
6. 落库。结果、usage、费用写入 run，source_texts 置空。

网关调用统一带浏览器 UA，5xx 与 429 退避重试三次。任一阶段失败，run 记 failed 和脱敏后的错误，失败信息带模型原文前 200 字，页面有「用当前画像重试」。任务执行器是进程内循环，`instrumentation.ts` 启动，每 3 秒用 `FOR UPDATE SKIP LOCKED` 领取一个 queued 的 run，单实例部署够用。

海报数据由 `scripts/seed-posters.mjs` 从仓库内 `data/posters-v0.3.jsonl.gz` 灌入，向量缺失时通过网关补算。进程启动后把 4538 条读进内存，余弦在 Node 里算。

## 模型

全部走 Mob AI 网关 `ai.mob-ai.cn`：`PROFILE_MODEL`（画像抽取，仅 files 路径用）与 `PICK_MODEL`（看图终选）默认均 `grok-4.6`；`EMBED_MODEL=jina-embeddings-v5-text-small` 是向量模型不在此列。立项时画像抽取用的是 `claude-sonnet-4-6`，2026-09-21 前改成全量 grok-4.6。

## 数据

| 表 | 字段 |
|---|---|
| app_users | id、display_name、email、avatar_url、role（member/admin）、active、auth_version、last_login_at、时间戳 |
| external_identities | user_id、provider（feishu/dev）、app_id、tenant_key、union_id、open_id、时间戳；(provider, app_id, tenant_key, open_id) 唯一 |
| app_sessions | token_hash、user_id、auth_version、method、expires_at、last_seen_at、user_agent |
| auth_oauth_states | state_hash、browser_hash、verifier、next_path、app_id、redirect_uri、expires_at |
| auth_audit_events | actor_user_id、target_user_id、action、details、created_at |
| posters | path 主键、title、year、added、tags JSONB、subject、motifs TEXT[]、embedding REAL[]、vocab_version、model、tagged_at |
| runs | id UUID、user_id、work_title、status、file_manifest JSONB、source_texts JSONB（抽完置空）、profile_auto JSONB、profile_final JSONB、profile_edited_by、profile_edited_at、results JSONB、usage JSONB、cost_usd、error、parent_run_id、时间戳 |
| api_tokens | 见上文认证模型 |

状态取值 queued、extracting、embedding、matching、picking、completed、failed。重跑生成新 run，parent_run_id 指向原 run，画像来自用户编辑后的版本，跳过抽取阶段。

### 数据保留

`runs.source_texts` 只在抽画像期间存在，处理路径结束即清空。长期保留 file_manifest（路径/大小/sha256）、`profile_auto`（机器抽取）、`profile_final`（人工确认版，生图接入必须用它）、results、usage、cost_usd。

## 额度与费用

每人每天 20 次匹配，按 Asia/Shanghai 计日，admin 不限。费用从网关返回的 usage.cost_usd 累加，缺失时按 token 估算并标注。实测数字按时间排列如下。

- 2026-09-10，Cornered Beast 导出包上传 8 个文件共 340 KB，整次 5 分钟，0.38 美元，其中 claude-sonnet-4-6 抽画像 0.34 美元。修改画像重跑跳过抽取，约 1 分钟，0.04 美元。
- 2026-09-21，生产 Bearer token 跑通提交导出文件夹到简报下载，245 秒完成，约 0.14 美元。直提 profile 约 0.03 美元。

## 环境变量

`APP_ENV`、`APP_ORIGIN`、`DATABASE_URL`、`SESSION_SECRET`、`LARK_LOGIN_APP_ID`、`LARK_LOGIN_APP_SECRET`、`LARK_LOGIN_TENANT_KEYS`、`MOB_AI_BASE_URL`、`MOB_AI_KEY`、`PROFILE_MODEL`、`PICK_MODEL`、`EMBED_MODEL`、`POSTER_SITE_BASE`、`ADMIN_BOOTSTRAP_NAME`。飞书回调地址 `/api/auth/feishu/callback`。

## 运维脚本

- `scripts/create-token.mts`：`DATABASE_URL=… node --import tsx scripts/create-token.mts --user <id|email|姓名> --name <备注> [--days N]`，不传 `--days` 永久有效；token 只打印一次。
- `scripts/upload-export.mts`：`--profile <画像.json>` 直提画像（推荐），或 `--dir <导出文件夹>` 走抽画像；另 `--token bcm_… [--base URL] [--title 名] [--wait]`，`--wait` 轮询至 completed/failed。
- 生产库本地访问：`railway connect Postgres --tunnel-only -P <port>`（SSH 隧道，用 Postgres 服务变量里的 PGUSER/PGPASSWORD）。

## 范围

底子是 Novel Library（`/Users/wangbo/Downloads/emotional_source/novel-library`），搬了它的飞书登录、会话、Postgres 访问、日志、请求代理、登录页、侧栏导航、浅色样式和 Inter 字体。栈是 Next.js 16 App Router、React 19、TypeScript strict、CSS Modules、pnpm、pg 裸 SQL 加增量迁移，不用 Tailwind、shadcn、Prisma。

本站不打标、不存图、不出图。参考图由使用者拿去喂 GPT Image 2。批量上传多个作品、海报库更新与重新打标、飞书通知、深色主题、导出 PDF 都不在范围内。

## 交接文档

- 2026-09-13，给 wangbo 拿去让 GPT 设计封面生成 skill 的短交接。飞书 https://xcn662409uz0.feishu.cn/docx/QinBda16EoZE3PxwmjGcbQAknke ，原文副本 [source](../../raw/2026-09-13-book-cover-match-handoff.md)。列了数据契约、三种拿结果的方式、六条已定边界、网关四个生图模型的参数、封面 skill 要回答的八个问题和已知的坑。
- 2026-09-21，完整的开发交接 `~/Downloads/Book Cover Match 与插画生成开发交接.md`，原文副本 [source](../../raw/2026-09-21-book-cover-match-handoff.md)。

## 关联

- 下游接入方：[[entities/lunaverse-ide]]（illustration 模板接入待做）
- 生图 skill：`LinghuC2333/generate-illustration-skills`；画风配置：`LinghuC2333/style_illustration_config`
- [[concepts/poster-reference-matching-tagging]] 匹配算法、词表、打标结果与实测数字
- [[entities/mob-ai-router]] 画像抽取、向量、终选都走这个网关
- [[entities/mobai-image-site]] 同一天立项的生图站，飞书流程同源
- 已验证：生产 Bearer token（王博Kaito admin，永久，name=`ide-api`）跑通"提交导出文件夹 → 245s 完成 → 三版本各 5 选 → 简报下载"，单任务成本约 $0.14。
