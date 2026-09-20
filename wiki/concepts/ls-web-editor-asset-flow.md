---
title: LS 在线编辑器 角色与素材流程设计
tags: [lunascripts, editor, assets, sprites, look-vocab, product-design]
sources:
  - /Users/wangbo/Downloads/IDE-for-ugc/docs/superpowers/specs/2026-09-17-assets-phase1-design.md
created: 2026-09-17
updated: 2026-09-17
---

# LS 在线编辑器 角色与素材流程设计

2026-09-17 wangbo 提出的问题。创作者写剧本时不知道立绘怎么命名、神态和动作有哪些可选，没有素材就没法 `@` 素材名，而服装又看起来要先生完素材才知道有哪些。本页记的是给 [[entities/ls-web-editor]] 的产品设计。第一期已于 2026-09-17 在分支 `feat/assets-phase1` 做完，见文末「第一期落地」。

## 判断

立绘名 `角色__服装__神态[-动作]` 只是标签，编译器不校验它，素材是生产阶段按标签批量生成的，文件名与标签一字不差。所以顺序可以反过来，先定名字后生图，创作者写作全程不该被素材卡住。桌面版 IDE 的 character-architect 和 outfit-anchor 两个阶段做的就是先定标签，词表在 `lunaverse-ide/agents/adaptation/skills/episode-writer/look_vocab.json`（v2，62 个神态词加动作词，每个词带中文释义和给生图模型的英文描述）。

## 结构（2026-09-17 wangbo 定稿）

一本书打开就是两半，剧情和素材，不另开 tab。素材的分类照编译器的素材映射表来，剧本里 `@` 到的每个名字在素材里有唯一位置。

```
书
├── 剧情            剧集树与编辑器
└── 素材
    ├── 图片
    │   ├── 背景       @bg set <名字>
    │   └── 角色立绘   按角色、服装分组，叶子是神态[-动作]，对应 @角色 <角色__服装__神态[-动作]>
    ├── 视频
    │   └── CG         @cg <名字>
    ├── 音频
    │   ├── 音乐       @music <名字>
    │   └── 音效       @sfx <名字>
    └── 小游戏         @minigame <名字>
```

角色不是独立页面，它是角色立绘这一类的分组方式。角色卡上有 ID、外貌描述、几套服装（自动带 `default`），新建角色、加服装都在这里。

两半靠名字咬合。素材页每一类的列表由剧本扫出的引用与已登记素材合并而成，剧本有素材没有的标「缺」，素材有剧本没用的标「未引用」。编辑器补全从素材页取候选，`@bg set` 补背景，`@` 补登记过的角色，再补服装，再补词表神态。引用了未登记的名字画黄线，一键修复跳到对应分类预填。全是提醒，不拦保存。

每项统一为名字、状态（缺、生成中、已有）、描述（生图提示词材料，立绘由外貌加服装加词表神态描述拼成）、文件地址。这张表就是编译用的 mapping.json。

页面左侧竖向分栏，顶部「剧情」「素材」两大项，各自展开树；右侧主区随选中项变化，选一集是编辑器，选分类是清单，选角色是角色卡。手机上左栏收成抽屉。

## 数据

Character(novelId, id, name, description, isProtagonist)、Outfit(characterId, key, description, isDefault)、Asset(novelId, kind, key, status, url)。Asset 表生成编译用的 mapping.json。

## 分期

1. 素材那半的结构与清单、角色登记、词表补全，缺项可导出提示词，不接生图
2. 接生图与上传，生成结果自动进映射表
3. 视频 CG 与小游戏的生产接入

## 第一期落地（2026-09-17）

设计文档在仓库 `docs/superpowers/specs/2026-09-17-assets-phase1-design.md`，实施计划 `docs/superpowers/plans/2026-09-17-assets-phase1.md`，10 个任务全部在分支 `feat/assets-phase1` 上完成，17 个提交。

实施时定下的几条规则，spec 里没写或写得不细。

- 素材登记接口只收背景、CG、音乐、音效、小游戏五类，立绘不能手工登记，只能由剧本引用产生
- 补全的第二段（服装）在角色只有一套服装时直接跳过，进神态列表；神态列表把本集最近用过的词排最前
- 四条黄线都只在编辑器拿到角色表时才报，playground 页没有角色表所以不报
- 词表随代码发布，改词表要改 `src/content/look-vocab.json` 再部署
- 只有状态为已就绪且有文件地址的素材才进映射表，目前没有生图和上传，所以线上映射表实际为空，编译行为和以前一致

未做的仍是分期表里的第二、三期，生图、上传、CG 与小游戏的生产接入。

## 第二期决策（2026-09-17 wangbo 定）

设计文档在 IDE-for-ugc 仓库 `docs/superpowers/specs/2026-09-17-assets-phase2-design.md`，分支 `feat/assets-phase2`。产品形态对齐 lunaverse-ide 的 Production Workshop（`packages/ls-workshop`）。

- 剧情与素材不放同一个目录树。书内加二级 tab，剧情和素材各带一套侧栏，分类树保留第一期结构
- 立绘走 ide 的三级链。角色人像、每套服装一张定妆图、每个神态一张立绘，下游拿上游当参考图。上游重生成后下游只标「参考图已更新」，不自动作废
- 素材状态照 ide 四态，缺、已生成、已通过、已打回。已生成和已通过都进映射表，通过只是审阅标记
- 生成走 mob-ai 网关 `/v1/generations` 异步模式。立绘链用 image-gpt 带参考图加透明背景，网关直接出透明 png，不做抠图。人像没有上游，先普通出图再拿自己当参考出透明版。背景和 CG 图用 image-seedream-pro，CG 视频用 video-seedance
- 生成图和上传图都存团队 R2，对象 key 为 `novels/<novelId>/<kind>/<assetKey>/<versionId>.<ext>`，每次生成或上传留一个版本可回滚
- 这一期不做音乐、音效、小游戏的生成，只留上传。不做封面和风格模板在线编辑

## 第二期落地（2026-09-17 夜）

实施计划 `docs/superpowers/plans/2026-09-17-assets-phase2.md`，16 个任务在分支 `feat/assets-phase2` 上做完，约 30 个提交。本地对着真实网关和 R2 跑通了整条链，背景（水彩）、人像（日系动画，两步透明）、定妆图、神态立绘、CG 图（厚涂）、CG 视频，风格选择对结果生效。

实施时发现并定下的事。

- 网关上 seedream 和 seedance 两个模型当天都在供应商侧失败（seedance 的上游 key 显示未激活），背景和 CG 图临时改用 image-gpt（2K、16:9），CG 视频改用 video-minimax-h3-fast（5 秒、768P、比例 adaptive）。模型名集中在 `src/server/gateway-models.ts`，恢复后改一处
- 网关异步提交的任务号在 `result.taskId` 和 `task.id`，失败原因在 `result.error.message`，成功结果在 `output.url`。带参考图生视频时 minimax 要求比例为 adaptive
- 本机没有直连 DNS，全走代理。S3Client 要挂 https-proxy-agent，Node 的 fetch 要开 `NODE_USE_ENV_PROXY=1`，Railway 上两者都是空操作
- 多个实现子代理共用一个工作区会在 git 暂存区上撞车，后来改成一次只跑一个实现者

## 第三期，对齐 ide 的画风包与人像链（2026-09-18）

设计文档 `docs/superpowers/specs/2026-09-17-assets-phase3-design.md`，计划 `docs/superpowers/plans/2026-09-17-assets-phase3.md`，分支 `feat/assets-phase3`，16 个任务。起因是 wangbo 指出网页版的六套风格和一步出人像都不是 ide 现在的做法，ide 的 origin/main（2026-09-18）已经是三个内置风格包加多段人像链，角色图由供应商原生出透明 png。

落地的事。

- 三个风格包 impasto-arcane、impasto-huan、flat-falling 原样复制进 `src/content/style-packs/`，参数从每个包顶层的 style.json 读，参考图由 `pnpm seed:style-packs` 幂等传到 R2 的 `style-packs/` 前缀下。书的风格改为三个 familyId，旧的六套删除
- 人像链改成四级，脸、人像、定妆图、立绘。选脸走 Legnext 的 Midjourney（`/v1/diffusion` 出四宫格，`/v1/upscale` 放大选中的一张，type 传 0），参数照包里的 stylize、chaos、styleWeight，arcane 是 catalog-reference 要角色的身份参考图并带 `--oref`，huan 和 flat 是 direct-generation。四宫格和单张都落 R2，因为 Legnext 链接七天失效。重抽一批会把脸退回待选并让下游标「参考图已更新」
- 人像分全身初稿、风格重绘、人体修复三个按钮，参考图顺序照 ide，全身风格板、人体比例图、已选的脸，重绘和修复再加初稿。角色图末尾追加 ide 的 native transparent alpha 契约原文。image-gpt 的透明模式要求至少一张参考图，带图时 quality high、2K、9:16 出 1152 × 2048 的 RGBA png
- 背景带包的背景板，CG 图带角色板和背景板加剧本里出场角色的人像，CG 视频带 CG 的图加风格板。minimax-h3-fast 最多收两张参考图，视频只带图加一张角色板
- 角色多了性别、年龄、身份参考图，身份图只认 R2 上本站的地址，识别到 Midjourney 参数样式的文本会被剔掉

本地对真实 Legnext、网关和 R2 跑过。flat_falling 走完整条链，huan 和 arcane 各选过脸。Midjourney 会对身份参考图做内容审核，用生成的动漫脸当身份图被拒过一次，换成包里的真人脸就过了。

## 待办，模特池选脸（2026-09-18 wangbo 决定暂不做）

ide 的选脸在出四宫格之前还有一步找模特。角色档案拼成 casting query 打 Lunaverse 网关的 `/api/ide/portrait-v2/casting/search`，后面接 Model Search v1，货源是 ModelManagement 的授权模特照片，返回最多三个候选，用户点一个当身份参考图，再拿它做 `--oref` 出脸。只有 catalog-reference 的画风（arcane）走这条线，18 岁以下角色不走。网页版第三期跳过了这一步，arcane 的身份图由用户自己上传。接进来需要一个能调该网关的服务端 token，或 Model Search v1 的地址和 key，wangbo 决定先不做。

## 第四期，立绘神态词库（2026-09-19 wangbo 定）

wangbo 看到角色页服装行只有一颗「生成缺的 N 张」，说用户根本不知道缺的是哪张，要的是一个能选神态动作的图库，和角色立绘词表对上。全书先统一挑一套，进到某个角色的某套服装还能单独加词、去词，最后每套服装得到一份明确的立绘清单。服装本身这期不做选购。

落地方式。词表照旧是 look-vocab.json 的 45 个神态词和 17 个动作词，图库里的单位是 token，`demeanor` 或 `demeanor-action`，动作挂在神态上是可选项，不做神态乘动作的全组合。存三个字段，`Novel.looks` 是全书默认，`Outfit.lookAdds` 和 `Outfit.lookRemoves` 是每套服装的增减，有效清单等于全书去掉 removes 再接上 adds。`listAssets` 把有效清单展开成立绘行（`fromLook`），所以侧栏计数、顶部「生成全部缺的」和角色页看到的是同一份清单，剧本里引用到的行照旧并进来。词库长出的行不预先落库，点生成时先登记再排队，和剧本引用的行走同一条路。

页面。「角色立绘」列表页多一个入口「神态动作词库」，路由 `/assets/sprites/looks`，45 张神态卡按词表顺序排，点卡选中，选中的卡下面可以「+ 动作」挂组合，底部固定一条「已选 N 张 · 保存到全书」。角色页每套服装一行，行尾两颗按钮，「增减神态」打开同一个组件的服装模式（卡片分全书、本套、已去掉三种态），「生成缺的立绘」先弹确认，把要生成的每一张按中文释义和 key 列出来再排队；定妆图没出图时列表里只有定妆图。每格标签是中文释义，剧本引用过的加「剧本」小标，格子菜单有「从这套去掉」。

评审里修掉的两处。已登记但剧本没引用的词库立绘原来会被算成「未使用」，会进清理和跳过补齐，改成只要在清单里就不算未使用。「从这套去掉」一次 PATCH 整份替换两个数组，连点两格会丢第一次，加了锁。

代码在 LinghuC2333/IDE-for-ugc 的 feat/assets-phase3 分支（PR #5 里），spec 是 docs/superpowers/specs/2026-09-19-sprite-looks-design.md。

## 生成图去噪（2026-09-19 wangbo 定）

wangbo 看线上立绘，皮肤和衣服上一片斑驳，一眼看出是噪点，要求所有生成图都去噪。查下来 gpt 出图不传 quality 时走 auto，线条发虚，先给五个图片阶段统一加了 quality: high，同一提示词对比锐度翻倍。去噪本身用 OpenCV 的非局部均值（fastNlMeansDenoisingColored，h=10），在线上那张立绘上比过 h=8/10/12/15、中值和双边滤波，h=10 把斑驳去干净、线条还在，h=15 开始塑料感。实现是 scripts/denoise.py，Node 侧 spawn 调用，静态图落地前过一遍，alpha 原样保留，视频和四宫格不碰；机器上没 cv2 就跳过原样存，版本参数里记 denoise: null。Railway 镜像加了 apk 的 py3-opencv。老图在大图里有一颗「去噪」按钮，压成一条新版本，版本列表标「去噪」。

ide 那边的对应做法不一样，它的立绘链最后是 process-cutout，把原图送 Modal 上的 Real-ESRGAN 放大两倍再抠图，靠放大把细节做硬。网页版没有那套 Modal 凭据，也不需要放大，所以只做去噪。
