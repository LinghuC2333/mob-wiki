# mobai 生图网站 设计说明

日期 2026-09-09。需求与约束以 [docs/product-design.md](../../product-design.md) 为准，那是用户交给我的交接文档原文。这份说明只记录交接文档留给实现方决定的事项，以及我把整个项目拆成几段的方式。改动这里的决定要同步改交接文档对应段落或在此注明差异。

## 一句话

给 mobai 团队做一个内部生图网站。成员用飞书登录，写提示词、选 Midjourney 模型、传参考图，后台通过 Legnext 接口生图，参考图和结果长期存在团队 R2，整套部署到独立 Railway 项目。

## 交接文档之外由我定下的事

### 名称与标识

- 包名 `mobai-image`，页面标题“mobai 生图”，侧栏用文字标识，不做新标志。
- Cookie 名 `mobai_session`，飞书浏览器绑定 Cookie 名 `mobai_feishu_browser`。
- 站内请求头 `x-mobai-user-id`、`x-mobai-user-role`、`x-mobai-user-name`、`x-mobai-workspace-id`，由 proxy 写入，进入时先删除同名外来头。

### 角色

只有两种角色，`member` 和 `admin`。交接文档里的“项目所有者”是部署侧身份，通过一次性脚本 `scripts/bootstrap-admin.mjs` 指定初始管理员，不是数据库角色。以后管理员可以把其他已登录成员提升为管理员，该动作写 audit_events。

### 本地开发的两个替身

线上只有飞书登录和 R2。本地没有这两样时项目也要能跑起来，所以加两个只在 `APP_ENV=development` 下生效的替身。任一替身在 `APP_ENV` 不是 development 时直接拒绝加载，并且启动时打印提示。

- 开发登录。`AUTH_DEV_LOGIN=true` 时开放 `POST /api/auth/dev-login`，用固定的开发 tenant_key 和传入的 open_id、姓名、角色建会话。生产构建里这个路由返回 404。
- 本地对象存储。`STORAGE_MODE=local` 时 presign 返回同源 `PUT /api/uploads/[id]/put?token=…` 地址，文件写到 `OBJECT_STORAGE_LOCAL_DIR`。签名读取链接也改成同源短期地址。`STORAGE_MODE=r2` 时走真实 R2 和 S3 签名。生产环境 `STORAGE_MODE` 必须是 r2。

### 模型与参数的冻结配置

`config/model-capabilities.json` 是页面和后台共用的唯一能力来源，字段含 `config_version`、`verified_at`、每个模型的 `enabled`、`provider_flag`、支持的参考类型、参数范围和费用倍率。初始内容按 2026-09-09 抓取的 `docs/vendor/legnext/mj-image-params.v1.json` 整理。

| model_id | 供应商参数 | 初始 enabled | 参考能力 |
|-|-|-|-|
| mj-v7 | `--v 7` | true，默认模型 | 画面参考 iw、风格参考 sref/sw、人物或物体参考 oref/ow（费用 2 倍） |
| mj-v8.2 | `--v 8.2` | true | 画面参考 iw、风格参考 sref/sw；不支持 oref/cref |
| mj-v8.1 | `--v 8.1` | false | 同 v8.2 |
| mj-v6.1 | `--v 6.1` | false | 画面参考、风格参考、人物参考 cref/cw |
| mj-v6 | `--v 6` | false | 同 v6.1 |
| mj-niji-6 | `--niji 6` | false | 画面参考、风格参考、人物参考 cref/cw，不支持 --no |

enabled 为 true 只表示页面可以选，`verified_at` 为空的模型在页面上标“未经本账户验证”。真实联调通过后再填日期。

参数范围写进配置，来源标注在配置文件的 `sources` 字段里。iw 0～3 默认 1、stylize 0～1000 默认 100、chaos 0～100 默认 0、seed 0～4294967295，这四项来自 Legnext schema。sw 0～1000 默认 100、ow 0～1000 默认 100、cw 0～100 默认 100，Legnext schema 只写了“Number”，这三项范围来自 Midjourney 官方文档，配置里标 `source: midjourney-docs`，联调时核对。

画面比例固定五个按钮，`1:1`、`2:3`、`3:2`、`9:16`、`16:9`。

### 提示词编译规则

编译器 `src/lib/generation/prompt-compiler.ts` 同时给 Web 校验和 Worker 提交使用，Worker 通过构建产物引用同一份代码。

- 输出格式为 `<画面参考URL列表> <用户提示词> --v 7 --ar 2:3 --iw 1 --sref URL --sw 100 --oref URL --ow 100 --s 100 --c 0 --seed N --style raw`。画面参考 URL 放在提示词最前面，这是 Midjourney 图片提示的位置。
- Raw 一律编译为 `--style raw`，不用 `--raw`。
- 用户提示词里出现任何 `--xxx` 形式的受控参数（`--v --version --niji --ar --aspect --iw --sref --sw --oref --ow --cref --cw --s --stylize --c --chaos --seed --style --raw --draft --hd --fast --turbo --q --quality --p --profile --bs`）时校验失败，提示改用设置区。解析按 token 做，不做字符串替换。
- 编译时记录 `compiler_version`，和原始提示词、结构化设置一起存库。含签名 URL 的完整 text 只进 request_attempts 的受限快照，展示和日志用去掉查询串的版本。

### 任务状态

按交接文档十个本站状态实现，状态机写在 `src/lib/generation/task-state.ts`，转移表在单元测试里逐条验证。供应商 `pending / staged` 映射为“排队中”，`processing / retry` 映射为“生成中”。

### 额度默认值

`quota_policies` 表里每个 workspace 一行默认策略，成员可单独覆盖。初始值每人每天 20 项、每人同时 1 项、团队同时 2 项，统计日按 `Asia/Shanghai`。这些值只是初始迁移里的默认，管理员页可改，缺少策略行时拒绝创建任务而不是放开。

### 测试策略

- 单元测试 `node --test tests/unit/*.test.mjs` 和 `*.test.ts`（通过 tsx 或先编译）。
- 数据库系统测试 `tests/system`，每次建独立临时库、跑迁移、跑完删库，沿用 Novel Library 的 helper 写法。本机 PostgreSQL 17 可用。
- 浏览器测试 Playwright，`tests/e2e`，用开发登录进入页面，验收 320、390、768、1280、1440 五个宽度。
- 供应商默认 mock。`PROVIDER_MODE=mock` 时 Legnext 适配器返回可控的假任务，能模拟成功、失败、402、429、超时和响应丢失。

## 分段

整个项目按下面五份计划推进，每份计划单独可运行、可测试。前四份不需要任何真实密钥。

1. 基础。项目骨架、锁定依赖、浅色设计系统与公共控件、数据库迁移与身份表、飞书登录、会话、proxy、开发登录、管理员初始化、页面壳子。
2. 生成链路。素材上传与校验、对象存储抽象、模型配置与提示词编译、Legnext 适配器与 mock、Worker 与任务表、生成 API、幂等与恢复、额度账本。
3. 页面。生图页、我的图片、生成记录、任务详情、我的页面，各种空态与错误态，手机布局。
4. 管理与可靠性。错误日志、管理员三页、停用与撤销会话、恢复保存、变化与放大、导出、告警提示。
5. 部署与联调。Railway 配置、真实飞书应用、R2 跨域、Legnext 小额联调、恢复演练。这一段要用户提供密钥和域名。

## 明确不做的事

交接文档列的后续排期项一律不做。此外本轮不做飞书外发通知、不做回调接收（首版靠 Worker 轮询）、不做深色主题、不引入 Tailwind 或 ORM。
