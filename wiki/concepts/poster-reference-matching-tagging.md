---
title: 海报参考匹配与打标方案（2026-09 立项，v0.2 已实施）
tags: [poster, tagging, lunaverse-stories, vision-model, retrieval]
sources: [raw/2026-09-08-poster-reference-matching-tagging-design.md, raw/2026-09-09-poster-reference-matching-v0.2-implemented.md]
created: 2026-09-08
updated: 2026-09-09
---

# 海报参考匹配与打标方案（2026-09 立项，v0.2 已实施）

Lunaverse Stories 每部作品出海报前需要几张契合的真实电影海报当参考，参考库是 wangbo 自建的 IMP Awards 海报站，4538 张、2020 到 2026 年。本页记录 2026-09-09 已经落地的 v0.2 管线和实测数字，grok-4.6 看图打标，jina 向量比对开放物件，grok-4.6 看图终选，全部走 Mob AI 网关，全量打标 10.19 美元约 3 小时。v0.1 的计划版见 [source](../../raw/2026-09-08-poster-reference-matching-tagging-design.md)，与本页不同处以本页为准。

状态 已跑通并完成全量。日期 2026-09-09。本版记录实际落地的管线和实测数字，v0.1 里的计划项凡与本版不同，以本版为准。代码在 `Lunaverse AppStore Promotion/poster-ref/`。飞书正式版 https://xcn662409uz0.feishu.cn/docx/UVSDdJCwJoxy0WxiupFch914neb

## 结论

4538 张 IMP Awards 海报已经全部打上匹配标签，两部样本作品 Cornered Beast 和 The Heir of Ash and Smoke 各出了一份参考简报。整条链路是 grok-4.6 看图打标，jina 向量比对开放物件，grok-4.6 看图终选，全部走 Mob AI 网关。全量打标花 10.19 美元，约 3 小时。

## 边界

- 只打匹配标签，构图、配色、字体、绘制风格一律不打。
- 人物结构只匹配人数和女男配比。
- 图片不下载到本地，缩略图只在内存里过一下再 base64 喂模型。
- 不用豆包，不用 Claude API，模型全部走网关 https://ai.mob-ai.cn/api。
- 参考图最终喂给 GPT Image 2 出海报，出图管线不在本方案内。

## 数据源

| 项目 | 情况 |
|---|---|
| 海报站 | https://impawards-posters-archive.1992155807.workers.dev，清单 /_metadata/transfer-manifest.jsonl，4538 行，主键用完整 path，36 个片名重复 |
| 缩略图 | /_preview/<path>，360×540 WebP，约 28 KB。站点会 403 Python 默认 UA，并发超过 18 路会重置连接，取图统一用 curl 加自定义 UA |
| impawards.com | 自身没有题材标签，海报页只有设计师、导演、演员和 IMDb 链接。题材要靠 TMDb 查，本版未接，先用模型猜 |
| Wattpad | 一级分类 23 个，题材页各有 20 个标签筛选条，作品侧词汇取自这里 |
| 作品侧 | 有 02-character-architect 时以 li-selection-decision 和各 bible 为准，只有 01 时从评估报告推断 |

## 标签集 v0.3

封闭字段由视觉模型从固定词表里选，开放字段自由写。relation、transfer、other_notes 三项已删。

| 字段 | 类型 | 取值 |
|---|---|---|
| figures | 封闭 | count 0 到 6，female、male、unknown 之和等于 count；拟人动物和类人角色算 unknown，普通动物和画中人物不算 |
| crowd | 封闭 | 远景人群或剪影群众 |
| age | 封闭 | child、youth、adult、mature、mixed、n/a |
| era | 封闭 | contemporary、period、medieval-fantasy、futuristic、timeless |
| setting | 封闭 | urban、interior、palace、forest、mountain-snow、water、beach、desert、sky-space、battlefield-ruins、rural、road、outdoor、stage-arena、void、other |
| mood | 封闭，主次各 1 | romantic、sensual、forbidden、dark、tender、epic、melancholy、playful、mysterious、defiant、energetic、other |
| genre_guess | 封闭，最多 2 | TMDb 的 17 个电影题材 |
| motifs | 开放，3 到 8 个 | 画面里有分量的具体事物，小写单数名词或两词短语，不写身体部位、片名、logo |
| subject | 开放 | 一句英文，12 词以内，说画面主体在做什么 |

物件改成开放提取的原因是封闭表在全题材海报库里 other 占到 60%，Peter Rabbit 的兔子和胡萝卜全成了 other，反光镜被凑成 mirror。开放之后同一张打出 rabbit、carrot、truck、denim jacket、side mirror。

## 模型选型

同一批 100 张分层抽样，四个模型各打一遍，人数、性别等字段以豆包 2.1-pro 为对照基线。

| 模型 | 成功 | 100 张费用 | 人数与基线一致 | 说明 |
|---|---|---|---|---|
| doubao-seed-2.1-pro | 100 | 约 0.4 美元 | 基线 | 会把画里的人、狗、雕像数进人数，且服务器拉不到 R2 链接 |
| grok-4.6 网关 | 100 | 0.25 美元 | 79% | 人数与 claude 一致率 92%，四张争议图肉眼核对全对 |
| claude-sonnet-4-6 网关 | 84 | 0.91 美元 | 79% | 失败为取图断连，非模型问题 |
| kimi-k3 网关 | 28 | 0.37 美元 | 75% | 一半请求上游报 Payment Required，四分之一输出非法 JSON |

定 grok-4.6 打标。物件比对用网关的 jina-embeddings-v5-text-small，1024 维，4538 张共 14 万 token。终选也用 grok-4.6。

## 打标流水线

脚本 `scripts/tag_posters.py`，产物 `data/tags-v0.3-grok-4-6-full.jsonl`。每张一次调用，curl 取缩略图进内存转 base64，走网关 chat/completions，response_format 用 json_object，提示词里附完整 schema，客户端再校验一遍并把物件归一去重。断点续跑以输出文件里已有 tags 的 path 为准。

全量实测 4538 张，10.19 美元，输入 650 万 token，输出 39 万 token。并发 12 路每分钟 13 张，18 路每分钟 17 到 22 张，24 路会把 R2 压出连接重置。跑了约 3 小时。中途遇到网关 503 一批，重试改为 429 和 5xx 等 10 到 50 秒后再试，最多五次。

## 全库分布

| 字段 | 分布 |
|---|---|
| 人数 | 0 人 392，1 人 1695，2 人 1061，3 人 395，4 人 274，5 人 196，6 人以上 525 |
| 时代 | contemporary 3259，period 592，timeless 568，futuristic 80，medieval-fantasy 39 |
| 主情绪 | dark 1131，mysterious 1049，playful 613，melancholy 377，energetic 367，defiant 329，tender 238，romantic 223，epic 130，sensual 57，other 21，forbidden 3 |
| 场景 | void 1796，outdoor 650，interior 463，urban 420，rural 256，water 180，forest 167，stage-arena 130 |
| 题材猜测 | drama 2060，thriller 1477，comedy 910，horror 727，mystery 542，romance 499，action 397 |

forbidden 只有 3 张，作品侧的 forbidden 在打分时视同 sensual、dark、romantic。medieval-fantasy 只有 39 张，奇幻作品的候选主要靠 period 和 timeless 补。

## 作品画像

画像 JSON 在 `works/` 下，字段与海报同一套，另按人数出三个变体，全员、主 CP、单人。Cornered Beast 是女 1 男 3 的当代雪山禁忌恋，画像物件 snow、log cabin、red ribbon、pine forest、pickup truck、fireplace、rifle。Heir 是女 1 男 1 的中世纪奇幻宫廷，物件 phoenix、fire、flame wings、golden collar、chain、black feather、crown、dragon、dagger、throne、arena。

## 打分与终选

脚本 `scripts/match_posters.py`。硬过滤为人数相等且题材有重合，候选不足 8 张时依次放宽到人数 ±1、放弃题材。软分满分 90 折算成 100。

| 项 | 满分 | 计分 |
|---|---|---|
| mood | 30 | 主对主 30，主情绪命中别名 22，主次交叉 15，次对次 8 |
| era | 15 | 相同 15，timeless 8 |
| setting | 10 | 相同 10，void 4 |
| motifs | 20 | 作品画像与海报物件加主体的向量余弦，按全池第 10 到第 99 百分位映射到 0 到 20 |
| age | 10 | 相同 10，mixed 5 |
| 性别配比 | 5 | 女男都相等 5 |

取前 24 张，其中前 16 张连缩略图交给 grok-4.6 挑 5 张，每张写两句中文理由。一次终选约 1 万 token。产物是每部作品目录下 `08-poster-reference/brief.md`，含画像、三个变体的终选 5 张原图链接和理由、全部候选表。

## 样本结果

Cornered Beast 全员版终选 All the Devils are Here、Mountainhead、On Fire、Needle in a Timestack、I was a Stranger，前两张都是一女三男加雪山木屋元素。双人版终选 Glacier National Park Romance、Wish You Were Here、Stars at Noon、Sanctuary、Someone Like You，规则分最高 80。Heir 双人版终选 Legend of the White Dragon、In the Lost Lands、Kiss of the Spider Woman、Wicked For Good、Die in a Gunfight，单人版终选 Wild Eyed and Wicked、Mother Mary、Cryptozoo、Avatar Fire and Ash、Red Sonja。

## 遗留与后续

- 题材仍是模型猜的，接 TMDb 只改查询侧，需要一个免费 API key 放进 `poster-ref/.env`。
- 同一场景最多 12 张的多样性上限还没实现。
- mood 缺一个 comic 类的词，衣着类物件在当代题材里偏噪声，下版词表调整。
- 新作品接入只需三步。在 `works/` 写画像 JSON，跑 `match_posters.py --pick --pick-n 5 --brief`，看简报。封装成 skill 待做。


## 相关页面

- [[entities/mob-ai-router]] 打标、向量和终选都走这个网关，key 与 UA 要求见该页
- [[concepts/find-your-li-content-redesign-2026-08]] 同属 Lunaverse 推广物料线，作品侧 bible 结构一致
- [[concepts/dream-rec-component-2-llm-tagger]] 已有的 LLM 打标组件

## Sources

- [v0.1 方案](../../raw/2026-09-08-poster-reference-matching-tagging-design.md)
- [v0.2 实施记录](../../raw/2026-09-09-poster-reference-matching-v0.2-implemented.md)
