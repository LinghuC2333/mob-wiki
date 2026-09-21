---
title: lunaverse-poster-ref（Book Cover Match）
tags:
- nextjs
- postgres
- railway
- poster
- api-token
sources:
- raw/2026-09-21-book-cover-match-handoff.md
created: '2026-09-21'
updated: '2026-09-21'
---

# lunaverse-poster-ref（Book Cover Match）

团队内部网站：成员用飞书登录，选一个 Lunaverse IDE 导出文件夹，网站从允许清单文件里抽作品画像，在 4538 张 IMP Awards 海报中匹配构图参考图，按全员（full）、主 CP（couple）、单人（solo）三个版本各挑 5 张并附理由和原图链接。

- 仓库：`LinghuC2333/lunaverse-poster-ref`（私有）
- 线上：https://book-cover-match.up.railway.app
- Railway 项目：`lunaverse-poster-ref`（Kaito Wang's Projects，production），含 `web` + `Postgres` 两个服务
- 本地端口：`http://127.0.0.1:3110`

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

## API 面

完整文档在仓库 `docs/api.md`。一览：

| 接口 | 说明 |
|---|---|
| `GET /api/me` | 验证 token / 当前用户 |
| `POST /api/runs` | 二选一：`{profile}` 直提 WorkProfile（推荐，跳过画像模型，从 embedding 起步）；或 `{work_title, files:[{path,text}]}` 走服务端抽画像。`size`/`sha256` 可选（服务端计算并校验）；路径可带顶层目录前缀；非允许清单文件跳过并在响应报 `skipped` |
| `GET /api/runs?q=` | 列自己的记录 |
| `GET /api/runs/{id}` | 状态、画像、结果、用量 |
| `POST /api/runs/{id}/rerun` | 提交修改后 `profile` 派生新任务 |
| `GET /api/runs/{id}/brief` | 下载 Markdown 简报（未完成 409） |
| `GET/POST /api/tokens`、`DELETE /api/tokens/{id}` | token 管理（仅网页会话） |

响应包装：成功 `{data, request_id}`，失败 `{error:{message}, request_id}`。

## 上传契约（2026-09-21 放宽）

原设计是浏览器 `webkitdirectory` 选文件夹、客户端算 sha256/size、且要求提交文件全部命中允许清单。放宽后服务端自己 `findRoot` 定位导出层、自算 sha256/size、跳过非清单文件——API 调用方可以把整个导出目录文本直接塞进来。限制：≤500 文件、请求合计 ≤12MB、清单内合计 ≤4MB、必须含 `01-novel-evaluator/evaluation-report.md`。允许清单见 `src/lib/poster/allowlist.ts`。

同日进一步开放 `profile` 直提：API 调用方提交符合 `WorkProfile` schema（`src/lib/poster/profile-schema.ts`，词表与海报标签同源）的结构化画像，校验后写入 `profile_auto`/`profile_final`，runner 从 `embedding` 起步——**不调画像模型**，单任务只剩 embed+pick 成本（实测 $0.03，约为文件路径的五分之一）。`profile` 与 `files` 互斥，同传返回 400。浏览器文件夹上传路径不变。

## 模型

全部走 Mob AI 网关 `ai.mob-ai.cn`：`PROFILE_MODEL`（画像抽取，仅 files 路径用）与 `PICK_MODEL`（看图终选）默认均 `grok-4.6`；`EMBED_MODEL=jina-embeddings-v5-text-small` 是向量模型不在此列。

## 运维脚本

- `scripts/create-token.mts`：`DATABASE_URL=… node --import tsx scripts/create-token.mts --user <id|email|姓名> --name <备注> [--days N]`，不传 `--days` 永久有效；token 只打印一次。
- `scripts/upload-export.mts`：`--profile <画像.json>` 直提画像（推荐），或 `--dir <导出文件夹>` 走抽画像；另 `--token bcm_… [--base URL] [--title 名] [--wait]`，`--wait` 轮询至 completed/failed。
- 生产库本地访问：`railway connect Postgres --tunnel-only -P <port>`（SSH 隧道，用 Postgres 服务变量里的 PGUSER/PGPASSWORD）。

## 数据保留

`runs.source_texts` 只在抽画像期间存在，处理路径结束即清空。长期保留 file_manifest（路径/大小/sha256）、`profile_auto`（机器抽取）、`profile_final`（人工确认版，生图接入必须用它）、results、usage、cost_usd。

## 关联

- 交接文档：`~/Downloads/Book Cover Match 与插画生成开发交接.md`（2026-09-21）
- 下游接入方：[[entities/lunaverse-ide]]（illustration 模板接入待做）
- 生图 skill：`LinghuC2333/generate-illustration-skills`；画风配置：`LinghuC2333/style_illustration_config`
- 已验证：生产 Bearer token（王博Kaito admin，永久，name=`ide-api`）跑通"提交导出文件夹 → 245s 完成 → 三版本各 5 选 → 简报下载"，单任务成本约 $0.14。
