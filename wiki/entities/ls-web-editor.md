---
title: LS 在线编辑器（IDE-for-ugc）
tags: [lunascripts, editor, web, nextjs, railway, ugc]
sources:
  - /Users/wangbo/Downloads/IDE-for-ugc/docs/superpowers/specs/2026-09-16-ls-web-editor-design.md
created: 2026-09-16
updated: 2026-09-17
---

# LS 在线编辑器（IDE-for-ugc）

给创作者用的网页版 Lunascripts 编辑器，目标是在浏览器里写完一本小说的全部剧集，手感对齐 Notion 和飞书的斜杠命令，文件始终是标准 `.ls` 纯文本，能和 [[entities/lunascripts]] 编译器与 [[entities/lunaverse-ide]] 无损互通。代码仓库 https://github.com/LinghuC2333/IDE-for-ugc（私有），本机路径 `/Users/wangbo/Downloads/IDE-for-ugc`，2026-09-16 立项。

设计文档在该仓库 `docs/superpowers/specs/2026-09-16-ls-web-editor-design.md`，实施计划在 `docs/superpowers/plans/2026-09-16-ls-web-editor.md`（15 个任务，每个带测试和提交点）。本页只记决策。

## 已拍板的选择（2026-09-16，wangbo）

| 问题 | 决定 |
|---|---|
| 编辑模型 | CodeMirror 6 文本编辑器加斜杠菜单。文件本体是 `.ls` 纯文本，不做 Notion 式块编辑 |
| 第一版范围 | 单集编辑加一本小说的分支目录树。不做播放预览、素材映射、协作、版本历史 |
| 持久化 | Postgres 加 Prisma 6.x，Google OAuth 登录（Auth.js v5） |
| 校验编译 | 浏览器内自写 TS 轻量解析器负责高亮、补全、行级即时诊断；权威校验和编译代理到 Railway 上现有的 lsc HTTP 服务 `moonshort-script-production.up.railway.app`，不把 Go 打进自己镜像 |
| 框架部署 | Next.js 16 App Router，pnpm，Dockerfile 部署 Railway，一个 web 服务加一个 Postgres |

## 关键设计

- TS 解析器只报有把握的规则，宁可少报不误报，错误码沿用 Go 校验器命名（MISSING_TERMINAL、INVALID_TRANSITION、INVALID_SIGNAL_NAME、CHECK_MISSING_FIELD 等）。Go 校验结果是整集级文本，不带行号，所以行级下划线只能靠 TS 层
- 2026-09-16 用托管 lsc 跑了 lunascripts 全部 testdata 和 contract fixtures，确认 `@bg name` 与 `@bg set name` 两种写法编译器都收，spec 只写了后者；`testdata/example/example-old-version.ls` 是旧格式，编译器自己也拒绝
- 指令元数据表 `src/ls/directives.ts` 是斜杠菜单、补全、高亮、诊断的唯一来源，每条带中英文别名和 CodeMirror snippet
- 斜杠菜单按光标所在块过滤。`@phone` 内只给 `@text`，`@gate` 内只给出口指令，brave 选项内才给 `check`
- 剧集表唯一键为 novelId 加 branchKey 加 seq，剧集 ID 字符串 `main:01`，导出目录结构就是 spec 的 `main/01.ls`
- 契约版本对齐 lunascripts 4.0.0，内心独白用 `INNER_THOUGHT:`，`YOU:` 只给 LEGACY_YOU 警告

## 进度（2026-09-16）

15 个任务在分支 `feat/ls-web-editor` 上做完，已推到 GitHub 并开了 PR #1 合 main。110 个 Vitest 加 3 条 Playwright 全绿，Docker 镜像本地构建并通过健康检查。还没做的两件事要 wangbo 在场：Google OAuth 客户端的 id 和 secret（没有它登录走不通，工作区页面没人在浏览器里真正用过），以及 `railway up` 部署。2026-09-17 已部署，线上地址 https://lunascripts.up.railway.app ，Railway 项目 lunascripts-editor（Kaito Wang's Projects 工作区，服务 web 加 Postgres）。部署方式是本机 `railway up --service web`，没有接 GitHub 自动部署，改完代码要手动再 up 一次。

实施中发现并写回 spec 的语法事实：角色立绘的 transition 和 `@bg` 共用一张表（fade/cut/slow/dissolve），Go 校验器只有一张表；`@bg name` 与 `@bg set name` 两种写法都收；`&` 可以作为一个块的第一行（Go 不报错）。

本地 Postgres 用宿主机 5434 端口，5433 是 moonshort-backend 的 noval-db-dev 在用。

已知遗留（都在分支的 SDD 台账里记过）：`next.config.ts` 没开 standalone，镜像偏大；覆盖导入时若 800 毫秒内恰有未保存击键会把草稿存回去；剧集树不支持移动分支。

## 语法手册

站内 `/guide` 页面（登录后可见）是给创作者看的 Lunascripts 手册，前半按写一集的顺序讲，后半一条指令一张卡。内容在仓库 `src/content/guide.ts`，每个例子都有测试跑解析器保证不画红线，对应契约 4.0.0。改语法时先改这个文件。

## 进度（2026-09-17）

- 分支 `feat/notion-style-ui`（PR #2）把界面改成 Notion 的设计语言，加了登录后才能看的语法手册 `/guide`，已部署到线上
- 分支 `feat/assets-phase1`（PR #3，合到 feat/notion-style-ui）做完素材层第一期，2026-09-17 已部署到线上，迁移由容器启动时的 prisma migrate deploy 执行。一本书分成剧情和素材两半，角色登记、三段立绘补全、四条黄线提醒、清单页与提示词导出。设计与规则见 [[concepts/ls-web-editor-asset-flow]]
- 合并顺序 PR #1、PR #2、素材层 PR，三条分支是串着开的
