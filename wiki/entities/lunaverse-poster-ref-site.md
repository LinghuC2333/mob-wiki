---
title: 海报参考站（lunaverse-poster-ref，2026-09 立项）
tags: [poster, lunaverse-stories, feishu-login, railway, nextjs, internal-tool]
sources: [raw/2026-09-09-poster-ref-site-design.md]
created: 2026-09-09
updated: 2026-09-10
---

# 海报参考站（lunaverse-poster-ref，2026-09 立项）

给 mobai 团队用的内部网站，成员用飞书登录，选一个 lunaverse ide 导出文件夹，网站抽出作品画像并在 4538 张 IMP Awards 海报里匹配参考图，每人每次匹配留记录，部署在 Railway。代码目录 `/Users/wangbo/Downloads/lunaverse-poster-ref`，底子是 Novel Library，匹配算法来自 [[concepts/poster-reference-matching-tagging]]。本页是设计说明的 wiki 版，设计原文见 [source](../../raw/2026-09-09-poster-ref-site-design.md)。

给 mobai 团队用的内部网站。成员用飞书登录，选一个 lunaverse ide 导出文件夹，网站从中读几个文本文件，抽出作品画像，在 4538 张 IMP Awards 海报里匹配出参考图，按人数版本各挑 5 张，附理由和原图链接。每个人的每一次匹配都留记录，管理员能看全团队。部署在 Railway。

匹配算法本身来自 `Lunaverse AppStore Promotion/poster-ref/` 已经跑通的 Python 版本，本站把它移到 TypeScript。算法说明见 wiki `concepts/poster-reference-matching-tagging`。

## 底子与栈

底子是 Novel Library，本地目录 `/Users/wangbo/Downloads/emotional_source/novel-library`，搬它的飞书登录、会话、Postgres 访问、日志、请求代理、登录页、侧栏导航、浅色样式和 Inter 字体。不搬密码登录、账号认领、作品库、评审、Worker、Codex。

Next.js 16 App Router、React 19、TypeScript strict、CSS Modules、pnpm、pg 裸 SQL 加增量迁移、Node 22 以上。不引入 Tailwind、shadcn、Prisma、ORM。

## 边界

- 服务器读不到成员的硬盘。成员在浏览器里选文件夹，浏览器本地筛出允许清单里的文本文件，只把这几个文件的正文发上去。图片、novel.txt、03 之后的目录不离开成员电脑。
- 服务器不保存源文件。正文只在抽画像期间放在 run 记录的临时列里，抽完立即清空。长期保留的是文件清单（路径、大小、sha256）、画像、结果、费用。
- 海报缩略图和原图直接引用 R2 站点，本站不存图。
- 模型全部走 Mob AI 网关。打标已经完成，本站不打标；只做画像抽取、向量、终选。
- 参考图之后由使用者拿去喂 GPT Image 2，出图不在本站范围。

## 允许清单

浏览器只读取并上传下面这些相对路径，其余一律忽略。单文件上限 1 MB，合计上限 4 MB，超出直接拒绝。

| 相对路径 | 用途 |
|---|---|
| `book.json` | 作品标题 |
| `01-novel-evaluator/evaluation-report.md` | 题材标签、世界观、判定、主线 |
| `01-novel-evaluator/relationship-map.md` | 人物关系与 LI 候选 |
| `01-novel-evaluator/storyline.md` | 关键场景，截取前 20 KB |
| `02-character-architect/li-selection-decision.md` | 正式 LI 名单 |
| `02-character-architect/mc-bible*.md` | MC 性别、年龄、外貌、关键道具，每份截取前 12 KB |
| `02-character-architect/li-bible*.md` | LI 性别、年龄、外貌，每份截取前 12 KB |

导出包顶层可能多一层以作品命名的目录，浏览器按“包含 01-novel-evaluator 的那一层”定位根目录。

## 页面

| 路径 | 内容 |
|---|---|
| `/login` | 「使用飞书登录」，仅 mobai 租户。开发环境多一个开发登录表单 |
| `/` | 选文件夹。选中后本地列出将上传的文件和跳过的数量，点「开始匹配」。下方是我最近的匹配 |
| `/runs/[id]` | 先作品画像，再三个人数版本的海报清单。画像区可编辑并「按修改重跑」。每个版本先终选 5 张（缩略图、标题年份、分数、理由、原图链接），再折叠全部候选和分项分。右上角「下载简报」 |
| `/runs` | 我的全部匹配，按时间倒序，状态、作品名、费用 |
| `/admin/runs` | 全团队匹配记录，按人、作品、日期筛，仅 admin |
| `/me` | 头像姓名、今日剩余次数、退出 |

结果页在任务未完成时每 2 秒轮询一次状态，四个阶段依次显示，抽画像、算向量、匹配、终选。

## 数据

| 表 | 字段 |
|---|---|
| app_users | id、display_name、email、avatar_url、role（member/admin）、active、auth_version、last_login_at、时间戳 |
| external_identities | user_id、provider（feishu/dev）、app_id、tenant_key、union_id、open_id、时间戳；(provider, app_id, tenant_key, open_id) 唯一 |
| app_sessions | token_hash、user_id、auth_version、method、expires_at、last_seen_at、user_agent |
| auth_oauth_states | state_hash、browser_hash、verifier、next_path、app_id、redirect_uri、expires_at |
| auth_audit_events | actor_user_id、target_user_id、action、details、created_at |
| posters | path 主键、title、year、added、tags JSONB、subject、motifs TEXT[]、embedding REAL[]、vocab_version、model、tagged_at |
| runs | id UUID、user_id、work_title、status、file_manifest JSONB、source_texts JSONB（抽完置空）、profile_auto JSONB、profile_final JSONB、profile_edited_by、profile_edited_at、results JSONB、usage JSONB、cost_usd、error、parent_run_id、created_at、started_at、finished_at |

状态取值 queued、extracting、embedding、matching、picking、completed、failed。重跑生成新 run，parent_run_id 指向原 run，画像来自用户编辑后的版本，跳过抽取阶段。

海报数据由 `scripts/seed-posters.mjs` 从仓库内 `data/posters-v0.3.jsonl.gz` 灌入，向量缺失时通过网关补算。进程启动后把 4538 条读进内存，余弦在 Node 里算。

## 流水线

1. 抽画像。把允许清单里的正文按固定顺序拼接，交给网关 `claude-sonnet-4-6`，按 JSON schema 输出画像。字段与海报同一套，genre、mood 主次、era、setting、motifs 开放词 6 到 14 个、subject 一句、cast（MC 与每个 LI 的姓名、性别、年龄段）、inference_notes（哪些是推断）。有 02 目录以 li-selection-decision 为准，没有就从评估报告推断并在 inference_notes 说明。
2. 变体。从 cast 派生 full、couple、solo 三个人数版本。full 与 couple 相同时只跑一次。
3. 向量。画像的 motifs 加 subject 用 `jina-embeddings-v5-text-small` 算查询向量。
4. 匹配。硬过滤人数相等且题材重合，不足 8 张依次放宽到人数 ±1、放弃题材。软分 mood 30（forbidden 视同 sensual、dark、romantic 得 22）、era 15、setting 10、age 10、性别配比 5、motifs 20（余弦按全池第 10 到第 99 百分位映射）。取前 24。
5. 终选。前 16 张的缩略图连标签交给网关 `grok-4.6` 挑 5 张，各写两句中文理由。缩略图由服务器从 R2 站点取，带自定义 UA，只在内存里过一下。
6. 落库。结果、usage、费用写入 run，source_texts 置空。

网关调用统一带浏览器 UA，5xx 与 429 退避重试三次。任一阶段失败，run 记 failed 和脱敏后的错误，页面给出可重试按钮。

任务执行器是进程内循环，`instrumentation.ts` 启动，每 3 秒用 `FOR UPDATE SKIP LOCKED` 领取一个 queued 的 run。单实例部署够用。

## 身份与权限

飞书授权码流程加 PKCE，state 单次消费并绑定发起浏览器，换到用户信息后核验 tenant_key 在 `LARK_LOGIN_TENANT_KEYS` 内。首次登录自动建 member。会话 token 只存散列，Cookie HttpOnly、Secure、SameSite=Lax，空闲 2 小时、绝对 12 小时。

管理员用 `scripts/bootstrap-admin.mjs` 指定，参数是 `feishu:<tenant_key>:<open_id>`，只匹配已经登录过的身份。首任管理员是 wangbo。member 只能看自己的 run，admin 能看全部。

开发环境 `APP_ENV=development` 且 `AUTH_DEV_LOGIN=true` 时开放 `POST /api/auth/dev-login`，生产构建返回 404。

## 额度与费用

每人每天 20 次匹配，按 Asia/Shanghai 计日，admin 不限。一次匹配约 0.3 美元，画像抽取占大头。费用从网关返回的 usage.cost_usd 累加，缺失时按 token 估算并标注。

## 部署

Railway 新项目，web 服务加 Postgres。`railway.json` 用 railpack，构建 `pnpm build`，部署前 `pnpm db:migrate`，启动 `pnpm start`，健康检查 `/api/health/ready`。首次部署后手动跑一次 `pnpm db:seed-posters`。

环境变量 `APP_ENV`、`APP_ORIGIN`、`DATABASE_URL`、`SESSION_SECRET`、`LARK_LOGIN_APP_ID`、`LARK_LOGIN_APP_SECRET`、`LARK_LOGIN_TENANT_KEYS`、`MOB_AI_BASE_URL`、`MOB_AI_KEY`、`PROFILE_MODEL`、`PICK_MODEL`、`EMBED_MODEL`、`POSTER_SITE_BASE`。飞书应用与 Novel Library、生图站共用，只需在飞书开放平台给新域名登记回调地址 `/api/auth/feishu/callback`。

## 测试

- 单元。允许清单筛选、变体派生、打分函数、画像 schema 校验，纯函数，node --test。
- 系统。本地 Postgres 建库跑迁移与灌库，用开发登录走完 POST /api/runs 到 completed 的全程，用 Cornered Beast 与 Heir 两个导出包做样本。
- 页面。浏览器里过一遍登录、选文件夹、结果页、记录页、管理员页。

## 不做

批量上传多个作品、海报库更新与重新打标、出图、飞书通知、深色主题、导出 PDF。


## 进度（2026-09-10 凌晨）

已提交并上线。仓库 https://github.com/LinghuC2333/lunaverse-poster-ref（私有），Railway 项目 lunaverse-poster-ref，站点 https://web-production-91885.up.railway.app ，飞书登录用独立应用 Book Cover Match（cli_aa0c059e0d799d0c 是 Novel Library 的，已换掉），App ID cli_aa2b2c2afcb8dcc4，可用范围全员，版本 1.0.0 免审发布，回调已登记。启动命令先跑迁移和海报灌库再起服务，管理员按 `ADMIN_BOOTSTRAP_NAME=王博` 在登录后自动提升。

- 代码 `/Users/wangbo/Downloads/lunaverse-poster-ref`，Next.js 16.3、pg、TypeScript strict，本地端口 3110，数据库 `lunaverse_poster_ref`。
- 端到端实测。Cornered Beast 导出包上传 8 个文件共 340 KB，抽画像约 30 秒到 90 秒，整次 5 分钟，费用 0.38 美元，其中画像抽取 0.34 美元（claude-sonnet-4-6 读 12.8 万 token），终选三次共 0.04 美元。抽出的阵容 Seren 加 Knox、Liam、Dean 与 li-selection-decision 一致。
- 修改画像重跑跳过抽取，约 1 分钟，0.04 美元。
- 已知坑。网关偶发返回非 JSON 导致抽画像失败，前两次试跑都撞上，第三次通过；现在失败信息会带模型原文前 200 字，页面有「用当前画像重试」。本地开发 localhost 与 127.0.0.1 的 Origin 校验已互认。
- 待 wangbo 做的事。用飞书登录一次站点，3 分钟内自动成为管理员；GitHub 集成没有新仓库权限，目前用 `railway up` 从本地部署，若要自动部署需在 Railway 控制台给 GitHub App 授权该仓库。

## 相关页面

- [[concepts/poster-reference-matching-tagging]] 匹配算法、词表、打标结果与实测数字
- [[entities/mob-ai-router]] 画像抽取、向量、终选都走这个网关
- [[entities/mobai-image-site]] 同一天立项的生图站，飞书流程同源，生图站与 Novel Library 共用飞书应用，海报站用独立应用 Book Cover Match

## Sources

- [设计说明](../../raw/2026-09-09-poster-ref-site-design.md)
