# 海报参考匹配 · 打标技术方案 v0.1

状态 待 wangbo 审定。日期 2026-09-08。范围 只做匹配标签，不打构图、配色、字体等借鉴类标签。对象 IMP Awards 海报库 4538 张，2020 到 2026 年。作者 Claude，材料来自网站接口、两部作品的导出目录和本轮讨论。

飞书正式版 https://xcn662409uz0.feishu.cn/docx/UVSDdJCwJoxy0WxiupFch914neb

## 1 这份方案解决什么

Lunaverse Stories 每部作品都要出一张海报，出图前需要几张契合的真实电影海报当参考。海报库只有片名和年份，小说侧有 evaluator 报告和 character bible，两边的语言对不上，没法直接检索。

方案分两段。第一段是一次性的离线打标，把 4538 张海报翻译成一套封闭词表里的标签。第二段是查询，把每部作品也翻译成同一套标签，按规则打分取候选，最后由视觉模型从候选里挑出五张并写理由。

本轮讨论定下的边界有三条。人物结构只匹配人数和性别配比，怎么摆属于构图，不打。绘制风格不打，立绘风格由角色资产决定。构图、配色、字体这类借鉴标签整体不打。

## 2 数据源与现状

### 2.1 海报库

| 项目 | 已核实的情况 |
|---|---|
| 站点 | https://impawards-posters-archive.1992155807.workers.dev，Cloudflare Worker 前端，图片在对象存储 |
| 清单 | /_metadata/transfer-manifest.jsonl，4538 行，每行含 key、bytes、sha256。这是打标的唯一入口，不需要爬分页 |
| 列表接口 | /api/posters?page=N&sort=recent 返回 JSON，字段有 path、title、year、added、bytes、url、thumb |
| 缩略图 | /_preview/<path>，360×540 的 WebP，单张约 28 KB，单次请求 0.9 秒左右 |
| 原图 | /<path>，jpg，单张 0.5 到 3 MB，全量约 7 GB |
| 年份分布 | 2020 年 507 张，2021 年 664 张，2022 年 657 张，2023 年 756 张，2024 年 723 张，2025 年 740 张，2026 年 399 张，另有 92 张挂在 2019 及更早 |
| 主键 | 36 个片名重复，例如 Alice、Inheritance、Last Call。主键必须用完整 path，不能用片名 |

### 2.2 作品侧输入

两部样本作品的目录结构不一样，查询侧要兼容两种来源。

| 项目 | The Heir of Ash and Smoke（1896） | Cornered Beast（同事交付版 2026-09-07） |
|---|---|---|
| 可用目录 | 只有 01-novel-evaluator，旧版格式，四条硬性条款，relationship-map 无 LI 表 | 01 到 03 齐全，02-character-architect 有 MC bible、三份 LI bible 和 li-selection-decision |
| 题材标签来源 | evaluation-report 基本信息，fantasy romance、royalty、enemies to lovers、Phoenix/Dragon fantasy、政治反抗 | evaluation-report 基本信息，当代爱情、enemies to lovers、forbidden love、雪山封闭同居、多男主关系 |
| 人数来源 | 报告正文的主 CP 描述，Adan 与 Nox，女 1 男 1，共 2 人 | li-selection-decision 最终结论，Seren 加 Knox、Liam、Dean，女 1 男 3，共 4 人 |
| 年龄与外貌 | 报告未给数值，需从正文推断 | bible 明确，Seren 18 岁，Knox 21 岁 |
| 世界与场景 | Lymia 王权，王庭、竞技场、乡间，Phoenix 火与 Dragon | 当代美国，科罗拉多雪山封路山庄，洛杉矶 |

结论是有 02 目录时以它为准，只有 01 时从评估报告的基本信息和主线描述里推，推出来的人数要在简报里标注为推断。

## 3 匹配维度与标签集 v0

下面这份词表是 v0，由本轮讨论整理，还没有对着海报库验证过。它的作用是给第 4 节的词表发现流程当起点，v1 才是封表版本。每个维度都是封闭单选或多选，打标模型只能从表里取值，另留一个 other 字段收容表外情况。

### 3.1 维度总览

| 维度 | 取值方式 | 回答的问题 | 小说侧怎么得到 |
|---|---|---|---|
| 题材族 genre | 多选，最多 2 | 海报一眼看上去是哪类片 | 评估报告题材标签，经第 6 节映射表转换 |
| 情绪 mood | 主次各 1 | 画面传达的第一和第二情绪 | 从题材标签和主线描述推断 |
| 人数 figures | 整数加性别拆分 | 可辨认的主要人形有几个，女几男几 | MC 加 LI 数量，性别来自 bible |
| 人群 crowd | 布尔 | 是否有不计入人数的远景人群或剪影群众 | 不产出，仅用于解释人数 |
| 年龄段 age | 单选 | 主要人物看上去的年龄段 | bible 年龄字段 |
| 时代 era | 单选 | 画面属于哪个时代 | 世界观描述 |
| 环境 setting | 单选 | 画面主要在什么地方 | 世界观与主要场景 |
| 核心物件 motifs | 多选，最多 5 | 画面里有分量的物件或符号 | 关键道具、能力、世界设定 |
| 人物关系 relation | 单选，候选维度 | 多人时彼此看上去是什么关系 | 关系线类型，如 enemies to lovers |
| 转移性 transfer | 0 到 3，过滤项 | 去掉明星脸和 IP 标识后画面还站不站得住 | 不产出，只用来过滤 |

人物关系是本轮新加的候选维度。它记录的是人物之间的语义关系，比如相拥还是对峙，和构图无关。enemies to lovers 与 forbidden love 是 Lunaverse 的高频套路，这一项如果准确会明显提高命中质量。它在抽样阶段试打，准确率不够就删。

转移性是过滤项，用来把靠明星脸和已有 IP 撑起来的海报压下去。它只有一个整数，判断成本很低。如果多余可以直接删掉，打分逻辑不依赖它。

### 3.2 词表 v0

| 维度 | 取值 |
|---|---|
| genre | fantasy, sci-fi, horror, thriller, action, romance, drama, comedy, period, war, crime, mystery, other |
| mood | romantic, tender, sensual, forbidden, menacing, dread, melancholy, epic, defiant, hopeful, playful, isolated, vengeful, mysterious, other |
| figures | count 取 0 到 6，6 表示六个及以上。female、male、unknown 三个整数，和等于 count |
| crowd | true, false |
| age | child, youth, adult, mature, mixed, n/a。youth 指十几岁到二十多岁，adult 指三十到四十多岁，mature 指五十岁以上 |
| era | contemporary, period, medieval-fantasy, futuristic, timeless |
| setting | urban, interior, palace, forest, mountain-snow, water, desert, sky-space, battlefield-ruins, rural, road, void, other |
| motifs | fire, smoke-ash, moon, sun, wings, feather, crown, throne, blade, gun, blood, flower, wolf, dragon, bird, snake, horse, chain-collar, mask, mirror, key, ribbon-fabric, snow, rain, water, storm, lightning, shadow, eyes, hands, embrace, kiss, castle, car, window, door, stairs, bed, letter, phone, skull, cross, clock, book, other |
| relation | romantic, hostile, allied, ambiguous, none |
| transfer | 0 完全靠明星脸或 IP 标识，1 大半靠，2 画面自身成立但有依赖，3 完全成立 |

词表里最不稳的是 mood 和 motifs。genre 与 era 是通行的电影分类，争议小。setting 比上一轮多了 rural 和 road，因为 Cornered Beast 这类山庄题材和公路片在 2020 年代海报里都不少。

## 4 词表定稿流程

v0 是拍脑袋列的，不能直接拿去打 4538 张。定稿分四步，每步都有可以核对的产出。

1. 分层抽样 300 张。每个年份按比例取，随机种子固定写进配置，抽样清单存成 sample-300.jsonl。
2. 开放词汇打标。对这 300 张跑一遍，维度和 v0 一样，但每个维度让模型用自由短语回答，不给词表。产出 open-vocab-300.jsonl。
3. 统计合并。把自由短语做小写和词形归一，按维度统计频次。规则是出现 3 次及以上的值进入候选，人工把同义词合并，比如 longing 并进 romantic。再把所有作品导出包里评估报告的题材标签汇总一遍，确认每个标签在映射表里都有落点。产出 vocab-v1.json 和一页合并记录。
4. 封表复核。用 v1 对同一批 300 张重打，统计 other 的占比。任何维度 other 超过 2% 就回到第三步补词，直到全部维度低于 2%。

v1 封表以后才进入第 5 节的全量打标。全量跑完如果某维度 other 又超过 2%，出 v1.1 并只对命中 other 的海报补打，不重跑全量。

## 5 打标流水线

### 5.1 目录与存储

所有产物放在一个独立仓库目录 poster-ref 下，不混进作品目录。

| 路径 | 内容 |
|---|---|
| data/manifest.jsonl | 网站清单原样，带下载时间 |
| data/thumbs/<sha256 前 2 位>/<sha256>.webp | 缩略图，按 sha256 分桶，避免文件名里的空格和特殊字符 |
| data/posters.db | SQLite。posters 表存 path、title、year、added、bytes、sha256、thumb_local。tags 表存 path、vocab_version、model、tags_json、tagged_at、raw_response |
| data/tags-v1.jsonl | 从 tags 表导出的平铺版本，给查询侧读 |
| vocab/vocab-v1.json | 封表词表，同时生成 JSON Schema 给结构化输出用 |
| prompts/tagger.md | 打标提示词，版本号和词表绑定 |
| review/contact-sheet.html | 抽样对照页，每张图旁边列出全部标签，供人工扫查 |

### 5.2 步骤

1. 拉清单。下载 transfer-manifest.jsonl，解析 key 得到 path、year、title，写入 posters 表。
2. 拉缩略图。8 路并发，失败重试 3 次，落盘后校验是 WebP 且尺寸为 360×540。缩略图用清单里原图的 sha256 命名，因为一一对应。
3. 打标。每张图一次调用，输入是缩略图加提示词加词表，输出是严格的 JSON。用结构化输出把 JSON Schema 交给 API 强制执行，枚举值在服务端校验，省掉解析和重试。提示词固定放在最前面并打缓存断点，只有图片在后面变化。
4. 校验入库。检查 female 加 male 加 unknown 等于 count，motifs 不超过 5，mood 主次不相同。不通过的记入 review 队列并重打一次，两次都不过就人工看。
5. 导出。生成 tags-v1.jsonl 和 contact-sheet.html。

### 5.3 模型与调用方式

这台机器上没有配 Claude API 的 key，arkcli 的火山平台账号已经配好。两条路线都能走，区别在质量、成本和是否要新配凭证。

| 路线 | 全量估算成本 | 批量接口成本 | 说明 |
|---|---|---|---|
| Claude Opus 5 | 约 70 美元 | 约 35 美元 | 质量最稳，词表发现阶段建议用它。需要新配 key |
| Claude Sonnet 5 | 约 28 美元 | 约 14 美元 | 全量候选。是否用它由第 8 节的一致率决定，不因省钱直接选 |
| Claude Haiku 4.5 | 约 14 美元 | 约 7 美元 | 最便宜，人数和题材大概率够用，mood 和 relation 风险大 |
| 火山 doubao 视觉模型 | 执行时用 arkcli pricing 查 | 无 | 凭证现成，走 arkcli understand。价格和结构化输出支持要在抽样阶段实测 |

成本按每张输入约 1800 token、输出约 250 token 估算，其中缩略图约 260 token，提示词和词表约 1500 token 且命中缓存后大幅打折。上表没有算缓存折扣，实际会更低。价格取自 2026-06-24 的 Claude API 价目，执行前复核一次。

建议的选法是词表发现和 100 张抽样用 Opus 5，抽样阶段同时用 Sonnet 5 打同一批 100 张，两者在人数、题材、时代三个维度上一致率达到 90% 就用 Sonnet 5 跑全量，否则全量也用 Opus 5。全量走批量接口，价格减半，也不用管并发限额。

### 5.4 时间估算

| 环节 | 机器时间 | 依据 |
|---|---|---|
| 拉缩略图 4538 张 | 10 分钟以内 | 实测单张 0.9 秒，8 路并发 |
| 开放词汇 300 张 | 15 分钟 | 实时接口，10 路并发，单次 6 到 12 秒 |
| 抽样 100 张两模型 | 10 分钟 | 同上 |
| 全量 4538 张实时接口 | 30 到 90 分钟 | 20 路并发约 40 分钟，受限于 key 的每分钟额度，额度低时接近 90 分钟 |
| 全量 4538 张批量接口 | 通常 1 小时内，上限 24 小时 | 批量接口异步返回，不占本机 |
| 人工扫对照页 | 每 100 张 10 到 15 分钟 | 只看人数、题材、时代三项是否离谱 |

### 5.5 失败处理

- 网络失败按指数退避重试 3 次，仍失败写入 failed.jsonl，不阻塞其他任务。
- 模型拒答。恐怖和血腥类海报可能触发安全拒答，响应的 stop_reason 会是 refusal。这类海报记为 refused，先跳过，全量结束后用备用模型统一补打。
- JSON 校验失败重打一次，第二次仍失败进 review 队列。
- 全部调用可断点续跑，以 tags 表里是否已有当前 vocab_version 的记录为准，重跑不会重复计费。

## 6 作品侧画像生成

查询侧的任务是把一部作品变成和海报同一套标签的 JSON，叫作品画像。生成用文本模型，输入是作品目录里的几份文件，输出要人能核对。

### 6.1 输入与优先级

1. 有 02-character-architect 时读 li-selection-decision 的最终结论拿 LI 名单，读 mc-bible 和各 li-bible 的第 0 节拿性别与年龄，读评估报告基本信息拿题材标签和世界观。
2. 只有 01-novel-evaluator 时读评估报告的基本信息、判定和故事大纲，从主 CP 和可用 LI 描述推 LI 名单，人数标注为推断。
3. 关键道具和能力从 bible 的关键道具段和评估报告的世界观里取，转成 motifs。Heir 对应 fire、smoke-ash、wings、feather、crown、chain-collar，Cornered Beast 对应 snow、ribbon-fabric，环境取 mountain-snow。

### 6.2 题材映射表

作品侧的题材标签比海报侧细得多，要一张映射表把它们落到 genre、mood、motifs 上。表按作品数量增长，每出现一个新标签补一行。

| 作品侧标签 | genre | mood 倾向 | motifs 倾向 |
|---|---|---|---|
| fantasy romance / romantasy | fantasy, romance | romantic, epic | 视世界观 |
| enemies to lovers | romance | forbidden, defiant | relation 取 hostile 或 romantic |
| forbidden love | romance, drama | forbidden, sensual | 无 |
| royalty | fantasy 或 period | epic | crown, throne, castle |
| werewolf / alpha mate | fantasy, horror | menacing, sensual | wolf, moon, forest |
| 当代爱情 / contemporary | romance, drama | romantic, melancholy | 无 |
| 雪山封闭同居 | drama, romance | isolated, sensual | snow，setting 取 mountain-snow |
| isekai / otherworld | fantasy | mysterious | door, mirror |
| 政治反抗 | action 或 drama | defiant | fire, chain-collar |

### 6.3 人数的三种变体

Cornered Beast 需要四个人的海报，四人海报在电影里不多，全部命中的候选可能不到二十张。所以每部作品生成三个画像变体，各自检索。

- 全员版。MC 加全部 LI，Cornered Beast 是 4 人，Heir 是 2 人。
- 主 CP 版。MC 加第一 LI，两部都是 2 人。
- 单人版。只有 MC，1 人。

三个变体各出五张参考，简报里分开列。最终用哪一种是出图时的决定，匹配阶段不替用户选。

## 7 打分检索与最终挑选

### 7.1 硬过滤

- figures.count 与画像相等。
- genre 至少有一个重合。
- transfer 大于等于 2。

硬过滤后候选少于 20 张时依次放宽。先允许人数差 1 并扣 15 分，再把 transfer 门槛降到 1。放宽的层级写进简报。

### 7.2 软打分

| 项 | 满分 | 计分 |
|---|---|---|
| mood | 30 | 主对主 30，主对次或次对主 15，只有次对次 8 |
| era | 15 | 相同 15，timeless 对任何值 8 |
| setting | 10 | 相同 10，void 对任何值 4 |
| motifs | 20 | Jaccard 相似度乘 20 |
| age | 10 | 相同 10，mixed 对任何值 5 |
| 性别配比 | 5 | female 和 male 都相等 5 |
| relation | 10 | 相同 10，ambiguous 对任何值 4。维度删除时这 10 分并入 mood |

取总分前 40 张进入终选。同一 setting 最多 12 张，避免候选全是同一种场景。

### 7.3 终选

把 40 张缩略图和作品画像一起给视觉模型，让它选 5 张并对每张写三句理由，说明题材、情绪和人数哪里对上，哪里不对。这一步是唯一用模型做判断的环节，成本是一次调用。终选不打借鉴标签，理由里也不要求它评论构图。

### 7.4 产出

每部作品在其目录下生成 08-poster-reference/brief.md，内容包括作品画像 JSON、三个变体各自的候选数、放宽层级、五张终选的原图链接、分数和理由。简报是给人看的，出图提示词不在本方案范围内。

## 8 验收标准与质量控制

| 检查项 | 通过线 | 怎么测 |
|---|---|---|
| 词表覆盖 | 每个维度 other 低于 2% | 封表复核 300 张统计 |
| 人数准确率 | 95% 以上 | 抽样 100 张人工数，与标签比对 |
| 题材与时代准确率 | 90% 以上 | 同上，人工判定 |
| 自一致率 | 人数 95%，题材 90%，mood 主情绪 75% | 同一批 50 张同模型打两遍比对 |
| 双模型一致率 | 人数、题材、时代 90% | Opus 5 与 Sonnet 5 打同一批 100 张，决定全量模型 |
| relation 去留 | 准确率 80% 以上保留 | 抽样里多人海报人工判定 |
| 端到端 | 两部样本作品各出一份简报，wangbo 看五张终选至少三张认可 | 人工评 |

mood 的通过线故意放低，因为情绪本身有主观性，它在打分里靠主次两档兜底。人数和题材是硬过滤，错一个就整张海报错，所以要求高。

## 9 里程碑

| 阶段 | 交付 | 时间 | 需要 wangbo 做什么 |
|---|---|---|---|
| M0 | 本方案审定，拍板第 10 节 | 今天 | 审定 |
| M1 | 清单入库，缩略图落盘，300 张开放词汇，词表 v1 与合并记录 | 半天 | 看合并记录，确认词表 |
| M2 | 100 张抽样两模型，对照页，一致率报告，定全量模型 | 半天 | 扫对照页 15 分钟 |
| M3 | 全量打标，tags-v1.jsonl | 机器时间 1 到 3 小时 | 无 |
| M4 | 画像生成、映射表、打分、终选，两部样本作品的简报 | 1 天 | 评简报 |
| M5 | 按评审调权重，封装成 skill | 半天 | 验收 |

## 10 待定决策

1. 模型路线。走 Claude API 需要新配一个 key，走火山 doubao 凭证现成但结构化输出和质量要实测。默认按 Claude 路线写，词表发现用 Opus 5。
2. relation 维度是否试打。默认试打，抽样后按 80% 准确率决定去留。
3. transfer 过滤项是否保留。默认保留。
4. 缩略图分辨率是否够。默认用 360×540，motifs 里的小物件可能漏。如果抽样发现 motifs 漏得多，改为拉原图缩到 1000 像素高，多花 20 分钟下载和约三成的 token。
5. 参考图最终给谁用。给设计师看还是直接进生图模型，这决定简报之后要不要再加一段出图提示词。本方案先只出简报。
