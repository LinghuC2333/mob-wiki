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
