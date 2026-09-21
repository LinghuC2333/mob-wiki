# Book Cover Match 与插画生成开发交接

交接对象为接手项目的开发同事。本文依据当前仓库、Railway 项目、线上健康检查和已完成的生图测试整理。线上检查时间为 2026\-09\-21 07:55 UTC。Railway 成员邀请已由王博另行发送，是否接受及实际权限需接手人确认。

# 先了解现在交付到了哪里

Book Cover Match 已部署，负责从作品资料中抽取画像、匹配电影海报，供用户挑选参考。插画生成 skill 和画风配置是另外两个独立仓库，已经跑过实际生图并获得用户认可。目前没有把网站、skill 和 IDE 的 illustration 用途完整接成线上流程。

|部分|当前状态|接手后要做什么|
|---|---|---|
|Book Cover Match 网站|Railway 运行中，健康检查通过，海报库 4,538 张|确认登录、项目权限、任务记录和部署来源|
|generate\-illustration\-skills|已改名，封面流程已验证，129 项程序测试通过|复现封面流程，再处理通用插画要求|
|style\_illustration\_config|三套画风，每套 8 个模板，39 次引用校验通过|按原结构接入，不复制成另一份提示词配置|
|Lunaverse IDE|本地源码的模板列表仍只有原有 7 项|补齐 illustration 的类型、保存、读取及调用支持|

此次交接以维护现有网站、复现已验收效果和准备后续接入为范围。文中的开发顺序是建议，不代表已经约定上线日期。

# 项目入口与权限

[线上网站](https://book-cover-match.up.railway.app/)

[Railway 项目 lunaverse\-poster\-ref](https://railway.com/project/27619f59-67f6-4d8b-a48f-2ddbf116224e)

[网站源码 lunaverse\-poster\-ref](https://github.com/LinghuC2333/lunaverse-poster-ref)

[插画生成 skill](https://github.com/LinghuC2333/generate-illustration-skills)

[画风配置与参考图](https://github.com/LinghuC2333/style_illustration_config)

[Lunaverse IDE 源码](https://github.com/MobAI-Inc/lunaverse-ide)

Railway 项目位于 Kaito Wang's Projects，环境为 production，包含 web 和 Postgres。网站、skill、画风配置三个仓库均为私有。Railway 成员邀请不自动授予 GitHub、飞书应用管理、MOB 图片接口或 R2 的访问权限，这几项需要分别确认。

本次核对的本地版本为网站 358ded4、skill bdb9393、画风配置 e1d977c。接手时先拉取远端最新代码并记录提交号。Railway 最新 web 部署 c15e32ba\-cefa\-40f4\-87c7\-1c76cb08642b 显示 SUCCESS；本次返回的部署信息没有源提交号，部署究竟绑定哪个提交仍需在 Railway Settings / Deployments 中核对。

# 用户已经确认的生成标准

- 人物身份优先。脸型、年龄、头发、体型及服装来自人物参考图，画风不能把人物换成风格样例中的角色。

- 构图参考控制人物占位、相对大小、视角及明暗。封面测试使用电影海报；需要依据作品世界观替换道具和环境，同时保留它们在构图中的作用。

- 画风配置只规定画法。男女原始画风参考只在制作或更新风格时合成一次，正常生图读取保存好的合图。

- 封面场景按需求突出女主和至少一名男主；扩展到普通插画后，人数、比例和是否需要人物应由任务决定。测试封面采用 3∶4，不把这个比例写成所有插画的限制。

- 每张默认一次生成加一次去噪，不自动追加重画。检查发现问题要记录，用户认可当前效果后结束。

去噪目标是减少颗粒、碎纹和无意义细线，同时保留画风主要线条与明暗。去噪也可能让 Arcane 的笔触变平滑，所以必须保留前后图供比较。

# 网站如何运行

成员通过飞书登录，选择 Lunaverse IDE 导出文件夹。浏览器按允许清单读取作品文本，创建匹配任务。服务端依次抽取画像、计算向量、筛选海报和完成终选，再返回全员、主 CP、单人三个版本，每个有候选列表、终选理由和原图链接。

```text
queued → extracting → embedding → matching → picking → completed
处理异常 → failed
```

用户修改画像后会基于修改内容建立新任务。后续插画接入应使用 profile\_final；profile\_auto 用于保留自动抽取结果，不能覆盖人工改动。匹配成功只代表参考推荐已生成，不代表用户已选定某张海报。生图还需要明确保存用户实际选择。

|位置|作用|
|---|---|
|src/app|页面与 API 路由|
|src/lib/poster/allowlist\.ts|导出文件筛选规则|
|src/lib/poster/profile\-extractor\.ts / profile\-schema\.ts|抽取并校验作品画像|
|src/lib/poster/matcher\.ts / picker\.ts|候选筛选与终选|
|src/lib/poster/runner\.ts|领取、执行和恢复匹配任务|
|src/lib/poster/runs\-store\.ts / brief\.ts|任务记录与简报|
|src/lib/auth|飞书登录、会话及管理员|
|db/migrations / scripts|数据库迁移、海报灌库和维护|

当前允许读取 book\.json，以及 01\-novel\-evaluator 中的 evaluation\-report\.md、relationship\-map\.md、storyline\.md，和 02\-character\-architect 中的 li\-selection\-decision\.md、mc\-bible\*\.md、li\-bible\*\.md。evaluation\-report\.md 必须存在，合计限制 4 MB，单文件限制见 allowlist\.ts。这里的上传入口用于作品文本，没有替代生图所需的人物立绘上传。

源文本会临时写入 runs\.source\_texts，在画像抽取成功或任务失败等处理路径中清空。长期保留文件清单、画像、匹配结果和用量记录。不要把“没有长期保留原文”理解成文本从未经过服务器或模型接口。

## 开发常用接口

|接口|用途|
|---|---|
|POST /api/runs|提交 work\_title 和 files，创建匹配任务|
|GET /api/runs?q=…|查询当前成员的记录|
|GET /api/runs/\{id\}|读取任务状态和结果|
|POST /api/runs/\{id\}/rerun|提交 profile，基于修改画像创建新任务|
|GET /api/runs/\{id\}/brief|取得海报参考简报|
|GET /api/health/live 与 /api/health/ready|存活、数据库及海报库检查|

业务接口使用现有登录会话和身份校验。通常成功响应包在 data 内并带 request\_id，错误响应带 error\.message。brief 成功时直接返回 Markdown 文件，任务尚未完成会返回 409。完整输入类型和权限逻辑以路由及 runs\-store\.ts 为准；上表只用于定位，不是新增的对外 API 承诺。

# 本地启动与 Railway 维护

网站 package\.json 指定 Node\.js ≥22、pnpm 10\.30\.3，当前依赖 Next\.js 16\.3\.0 和 PostgreSQL。先准备本地 PostgreSQL，再按仓库 README 执行。以下为现有启动命令，本次交接没有新建数据库或执行付费系统测试。

```bash
git clone https://github.com/LinghuC2333/lunaverse-poster-ref.git
cd lunaverse-poster-ref
pnpm install
createdb lunaverse_poster_ref
cp .env.example .env.local
# 修改 DATABASE_URL，填写测试环境需要的配置
pnpm db:migrate
pnpm db:seed-posters
pnpm dev
```

本地入口为 http://127\.0\.0\.1:3110。APP\_ENV=development 且 AUTH\_DEV\_LOGIN=true 时可使用开发登录。线上必须使用飞书登录。仓库 \.env\.example 中的数据库用户名是本机示例，需要改成自己的账号。

|配置项|用途与交接方式|
|---|---|
|APP\_ENV / APP\_ORIGIN|线上 production；APP\_ORIGIN 为公网来源，不带路径|
|DATABASE\_URL / SESSION\_SECRET|数据库连接与会话密钥，从已授权环境配置中获取|
|LARK\_LOGIN\_APP\_ID / LARK\_LOGIN\_APP\_SECRET / LARK\_LOGIN\_TENANT\_KEYS|独立飞书应用 Book Cover Match 和允许登录的租户|
|MOB\_AI\_BASE\_URL / MOB\_AI\_KEY|画像、向量与终选所用网关，密钥单独交接|
|PROFILE\_MODEL / EMBED\_MODEL / PICK\_MODEL|源码默认 claude\-sonnet\-4\-6 / jina\-embeddings\-v5\-text\-small / grok\-4\.6；线上覆盖值需在 Railway 确认|
|POSTER\_SITE\_BASE|海报原图与缩略图来源，源码默认现有 Cloudflare 海报站|
|DAILY\_RUN\_LIMIT / RUNNER\_CONCURRENCY|默认每人每天 20 次、单进程并发 2；线上值需确认|
|ADMIN\_BOOTSTRAP\_NAME / ADMIN\_BOOTSTRAP\_IDENTITY|授予指定飞书成员管理员权限，按现有身份规则配置|

飞书登录回调应匹配 APP\_ORIGIN \+ /api/auth/feishu/callback。密钥、数据库连接串、R2 签名链接和 Railway 邀请链接不放进本文或 Git 仓库。

railway\.json 使用 RAILPACK，构建命令 pnpm build，启动命令 pnpm start。start 先做数据库迁移和海报灌库，再启动网站；库已完整时灌库跳过。健康检查路径为 /api/health/ready，超时 120 秒，失败重启策略上限 10 次。

本次线上 live 和 ready 均返回 HTTP 200，ready 返回 ok=true、posters=4538、runs\_24h=\{\}。这证明检查时基础服务和数据库可读，没有新跑一遍飞书登录、作品上传和模型匹配。

## 部署和恢复时重点看什么

- 先确认 Railway 的 GitHub 来源、分支、自动部署规则和当前生产提交。部署前保留数据库备份，记录上一版本；回退网站版本不会自动撤销数据库迁移。

- runner 在 web 进程中运行，默认每 3 秒领取任务。启动时会重新排队超过 2 分钟的中间状态任务，运行中以 20 分钟判定旧任务。改并发或扩容前先检查这些规则，避免重复执行。

- 按 request\_id 或 run id 查日志，重点看 runner\_started、run\_stage、run\_failed、run\_completed。先判断卡在抽取、向量、匹配还是终选，再处理对应依赖。

- 未来图片生成不能直接照搬“超时就重新排队”的规则。提交结果未知时必须先查询原 taskId，防止重复付费。

# 画风配置应怎样接入

style\_illustration\_config 按 IDE 风格家族组织，每个 style\.json 只有 familyId、displayName、model、templates。家族 ID 为 impasto\_huan、impasto\_arcane、flat\_falling。三个配置的 model 均为 image\-gpt。

```text
style_illustration_config/
  styles.json
  impasto_huan/style.json + refs/
  impasto_arcane/style.json + refs/
  flat_falling/style.json + refs/
  illustration-review.html
  tools/validate.py
  tools/build_overview.py
```

templates 中保留 base、scene、outfits、edit、cg\-image、cg\-video、cg\-manga，并新增 illustration。每个模板只含 prompt 和 refs，每条参考只含 file 和 sha256，图片路径相对当前画风目录下的 refs。styles\.json 只是包的索引。

```json
{
  "familyId": "flat_falling",
  "displayName": "flat-falling",
  "model": "image-gpt",
  "templates": {
    "illustration": {
      "prompt": "画风提示词与 {{prompt}} 占位符",
      "refs": [
        {
          "file": "illustration-26ad95871b03.png",
          "sha256": "从仓库原配置读取，不填本示意值"
        }
      ]
    }
  }
}
```

上面的结构只节选 illustration，不是可直接导入的完整配置。正式接入直接读取仓库原文件，保留原有七项。不要把人物身份、固定人数、3∶4、全身站姿或透明背景塞进 illustration 的通用画风提示词。

本次查看 IDE 本地源码，packages/ls\-workshop/src/custom\-style\-store\.ts 的 CUSTOM\_STYLE\_VARIANTS 仍为七项，platform\-adapter\.ts 的保存模板循环也未包含 illustration。开发需要连同类型定义、界面选项、读取、保存、导入导出、网关映射和实际传图一起检查；只新增 JSON 字段可能在保存时丢失。正式注册表本轮未修改。

# 插画 skill 怎样复现

generate\-illustration\-skills 的名称和 README 已更新，但 SKILL\.md 及准备、检查脚本仍包含封面专属要求。当前应先按封面用例复现，不能据此宣称已支持任意人物数量、纯环境图或所有插画任务。

```bash
git clone https://github.com/LinghuC2333/generate-illustration-skills.git
git clone https://github.com/LinghuC2333/style_illustration_config.git
cd generate-illustration-skills
python3 -m pip install -r requirements.txt
python3 -m unittest discover -s scripts -p "test_*.py"
python3 ../style_illustration_config/tools/validate.py
```

为每次任务建立独立目录，放入作品说明、选定的电影海报、实际人物立绘、画面提示词和 task\.json。task\.json 的相对路径从任务目录计算；画风图片由 style\.json 自动加入，不要在 references 中重复添加。

```json
{
  "style_config": "/path/to/style_illustration_config/flat_falling/style.json",
  "scene_file": "scene.txt",
  "aspect_ratio": "3:4",
  "cast": [
    "MC",
    "LI"
  ],
  "reference_limit": 4,
  "references": [
    {
      "role": "composition",
      "source": "poster.png",
      "subject": "用户选定的电影参考图"
    },
    {
      "role": "identity",
      "source": "mc.png",
      "subjects": [
        "MC"
      ]
    },
    {
      "role": "identity",
      "source": "li.png",
      "subjects": [
        "LI"
      ]
    }
  ]
}
```

示例中的 /path/to 需要换成实际位置。style\_config 可以使用绝对路径；若改用相对路径，则从 task\.json 所在目录计算。reference\_limit 是本次服务可接收的上限，示例四张由一张画风图、一张海报和两张人物图组成，不是所有接口的固定上限。

```bash
python3 scripts/prepare_cover.py --task /path/to/task.json --out /path/to/round
python3 scripts/prepare_cleanup.py --image /path/to/round/cover.png --style-config /path/to/style.json --aspect-ratio 3:4 --out /path/to/cleanup
```

这些脚本只准备输入，不会直接调用图片模型。完整流程由执行助手读取 SKILL\.md，查看素材，固定要求，实际调用生图服务，再准备并执行一次去噪，最后独立复核。review\_gate\.py 提供 freeze、register、evaluate、release 命令，只有符合脚本条件的结果才能标记自动验收通过。用户认可要另外记录。

# 已跑通的图片接口与 R2 用法

历史测试使用 MOB 网关 POST https://ai\.mob\-ai\.cn/api/v1/generations，请求模型别名 image\-gpt，异步 mode=async。返回 provider 为 gpt\-image2，未返回可核实的实际上游模型 ID。这是已成功使用的接口形式，本次交接没有再次发起付费生图；正式产品应对照网关当前契约验证。

```json
{
  "model": "image-gpt",
  "mode": "async",
  "input": {
    "prompt": "本次完整提示词",
    "references": [
      {
        "type": "image",
        "url": "服务能访问的图片地址"
      }
    ],
    "aspectRatio": "3:4"
  }
}
```

提交后先保存 task\.id；之后向同一接口发送 model=image\-gpt、mode=async、input\.taskId 查询状态。历史返回成功时从 output\.url 下载图片。认证使用 Bearer Key，凭据放服务端配置。不要把图片接口与网站抽取画像所用模型混成同一个模型配置。

历史测试先将输入图上传 R2，核对内容摘要，再用有时效的签名 URL 提供给图片服务。接入需要单独配置 R2\_ENDPOINT、R2\_BUCKET、R2\_ACCESS\_KEY\_ID、R2\_SECRET\_ACCESS\_KEY，以及需要时的 R2\_REGION、R2\_PREFIX。这些来源于本地已跑通的测试工具，不能假设 Railway 的网站已经配置好。

去噪使用原成图作为一张参考图，发送一次编辑要求。当前验证没有使用真正的 mask 参数，也不能保证像素位置完全锁定。曾经生成的构图图或手工蒙版不是本次稳定流程的必备步骤。结果必须检查真实宽高，接口给出的尺寸可能与上一张不同。

建议产品保存任务业务 ID、输入摘要、提示词、按序参考清单、远端 taskId、状态、结果地址及真实宽高。未返回费用或上游模型 ID 时保存空值。请求超时不能自动重新提交；先恢复原任务。密钥和完整签名 URL 不写入可分享日志。

# 已认可的效果与剩余偏差

三种画风均有王博认可的封面测试。认可的是这些具体结果，不是所有题材与构图都已经验证。Arcane 和 Flat Falling 使用旧 Cornered Beast 对应画风的立绘，两套男主造型不同，因此这组只能验证各自参考遵循，不能当作完全相同人物条件下的严格画风对照。

impasto\-huan 已认可测试封面

![Image](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NjFmOGIzZDg5NDUxODc5NGU4ZjViNWM3NmM4NTIyMzZfODRjMGVhM2ZiZDE1ZTVkNWIwZjNmNDllNzdjNzY5YzRfSUQ6NzY4Nzg5NTQ0NzQ4Njk3NTE2NF8xNzg5OTc5OTU5OjE3OTAwNjYzNTlfVjM)

impasto\-arcane 一次生成、一次去噪后的测试封面

![Image](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MmViNWMyNTAyNTFlNzk0OWRlOWQwOTM4NzRiZDhiNDRfMDYxYTRmM2NlNWNhMWQwNGRjMDVmZGFmNThjMTgyMGNfSUQ6NzY4Nzg5NTQ1MzQ1NTk3NzY5NV8xNzg5OTc5OTU5OjE3OTAwNjYzNTlfVjM)

flat\-falling 一次生成、一次去噪后的测试封面

![Image](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=OTY5ZDA3MjNkMjM2YmRlNGQyY2MxZTA0NmE4MjIyMWFfMTExMGUzNjdhMDRhNzYzMDE2OGYwNDBjMDAyY2ZlOWNfSUQ6NzY4Nzg5NTQ2NDIxMzM2ODAxNV8xNzg5OTc5OTU5OjE3OTAwNjYzNTlfVjM)

严格检查仍发现电影暗部、裁切及材质差异。Flat Falling 的衣服细节偏多；Arcane 去噪后较平滑。用户已认可并停止重画，不应为了把检查分数改成全通过而继续消耗调用。Arcane 去噪结果为 768×1024，Flat Falling 为 1104×1472，均为 3∶4；如产品要求统一像素尺寸，需要另行定义输出规则。

# 建议按这个顺序接手

|阶段|工作|完成的标志|
|---|---|---|
|接手现有服务|接受 Railway 邀请，取得三个仓库及所需凭据权限，核对生产来源和数据库备份|能登录网站，查看一个已有匹配记录，找到部署与日志|
|复现已有封面|使用本文参考样例的流程，在独立测试目录跑一个小样，控制为一次生成加一次去噪|实际传入全部参考图，有任务编号、原图、去噪图和检查结果|
|接入 illustration 配置|补 IDE 类型、模板枚举、保存读取和传图，继续保留旧七项|选择三种画风后读到正确 prompt 和 refs，保存重开不丢字段|
|连接网站和生图|记录用户选定海报，携带 profile\_final、角色映射、人物图和画风 ID，交给生图任务|用户可以从自己选择的参考进入生图，并查看状态与结果；失败不会重复扣费提交|
|通用化与回归|把电影海报、MC/LI 和封面规则改为用途相关条件；按实际输入选择检查项|单人图、双人图、纯环境图及无构图参考任务均有明确测试，原有封面流程不退化|

建议首阶段保留已经接受的提示词和画风图，先让开发环境复现结果。不要同时改画风、参考图、网关与检查标准，否则出现偏差时难以判断来源。

## 上线前至少确认

- 人物图、画风图和构图图的职责及顺序与实际请求一致，人物身份优先。

- 超时后能查到原任务，刷新页面或服务重启不会额外创建生图任务。

- 一次生成加一次去噪的默认上限可执行，用户另行要求修改时才扩展预算。

- 保留去噪前后结果，检查最终文件，用户验收和自动检查状态分开。

- 新增 illustration 不影响已有七种模板；旧作品与已保存画风能够继续读取。

# 尚待接手人确认的事项

- Railway 邀请是否已接受，具体项目和服务权限是否足够；GitHub 与飞书应用管理权限需单独核对。

- 生产部署的源码分支与提交、变量覆盖值、数据库备份策略及恢复方式，本次只核查了部署状态和健康检查。

- 图片调用权限、费用归属、限额与 R2 访问权限。本文不包含任何密钥或邀请令牌。

- 网站与 IDE 谁提供用户选海报、选人物和启动生图的入口，以及上线先后顺序。当前交接没有替开发确定工期。

- 完整本地测试包不在两个发布仓库中，包含作品素材、任务记录和检查报告。如需精确重放，请向王博索取对应样例包；本文图片可用于看效果。

本地已完成测试目录名为 api\-cornered\-standard\-01、api\-selected\-two\-books\-01 和 illustration\-cornered\-test\-01。不要把开发机器上的绝对路径写进产品配置。

# 查阅来源

[网站 README、部署配置和实现](https://github.com/LinghuC2333/lunaverse-poster-ref)

[skill 的 SKILL\.md、references 和 scripts](https://github.com/LinghuC2333/generate-illustration-skills)

[画风配置、查看页和校验工具](https://github.com/LinghuC2333/style_illustration_config)

[线上 readiness 检查](https://book-cover-match.up.railway.app/api/health/ready)

程序验证与文件校验于本次交接时重新执行通过；生图效果和网关参数来自已完成的历史测试。本次没有改动生产服务、部署、IDE 注册表或已有检查结论。

