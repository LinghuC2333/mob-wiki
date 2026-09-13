Book Cover Match 交接 · 海报参考站与封面生成的接口 
交接日期 2026-09-13。交接人 Claude，接收人 wangbo，用途是拿去让 GPT 设计「封面生成 skill」。这份文档只讲下一个设计者需要知道的事，代码细节看仓库。站点 https://book-cover-match.up.railway.app ，仓库 github.com/LinghuC2333/lunaverse-poster-ref（私有），本地目录 ~/Downloads/lunaverse-poster-ref。  
这个项目做到了哪一步 
Book Cover Match 是 mobai 团队的内部网站。成员用飞书登录，在浏览器里选一个 lunaverse ide 导出文件夹，网站从中读几份文本，抽出这部作品的画像，再到 4538 张 2020 到 2026 年的 IMP Awards 电影海报里找参考图，按全员、主 CP、单人三个人数版本各挑 5 张，每张给两句中文理由和原图链接。每一次匹配都留记录，管理员能看全团队。 
它的产出是「参考图清单」，不是封面。封面生成是下一步，也就是你要让 GPT 规划的那个 skill。本站的边界在这里停住，出图不在站内做。 
事项    现状    
上线    2026-09-10 上线 Railway，项目名 lunaverse-poster-ref，一个 web 服务加一个 Postgres。域名 book-cover-match.up.railway.app，还没挂自定义域名   
登录    飞书应用 Book Cover Match，App ID cli_aa2b2c2afcb8dcc4，可用范围全员，版本 1.0.0 已发布。只认 mobai 租户，首次登录自动建成员。姓名以「王博」开头的飞书身份登录后 3 分钟内自动提成管理员   
海报库    4538 张全部打好标签和向量，随仓库带在 data/posters-v0.3.jsonl.gz，启动时自动灌库，不用手动操作   
费用    一次匹配约 0.3 到 0.4 美元，画像抽取占大头。打标是一次性的，已花 10.19 美元，不会再发生   
部署方式    本地 railway up -s web --detach。GitHub 集成没给这个仓库授权，所以不是推代码自动部署   
样本    Cornered Beast 和 The Heir of Ash and Smoke 两部作品跑通，简报在各自导出目录的 08-poster-reference/brief.md   
链路一共五步 
下一个 skill 接的是第五步的产物，但前四步决定了产物长什么样，所以放在一起说。 
步骤    模型    做什么    
1 打标    grok-4.6，走网关    一次性离线完成。每张海报出人数与男女、年龄段、时代、场景、情绪主次、开放物件词、一句主体描述、题材猜测。物件是自由写的名词，不查表   
2 抽画像    claude-sonnet-4-6，走网关    把导出包里的评估报告、关系图、故事线、LI 决策、MC 与 LI bible 拼在一起，按固定 schema 输出作品画像。有 02 目录以 li-selection-decision 为准，没有就推断并写进 inference_notes   
3 向量    jina-embeddings-v5-text-small，走网关    画像的物件加主体描述算一个查询向量，和海报的向量算余弦，作为物件相似度   
4 打分    规则，不调模型    硬过滤人数相等且题材有重合，不够 8 张依次放宽到人数 ±1、放弃题材。软分情绪 30、时代 15、场景 10、年龄 10、性别配比 5、物件 20，取前 24   
5 终选    grok-4.6，走网关    前 16 张缩略图连标签交给模型挑 5 张，每张写两句中文理由。缩略图只在内存里过一下，不落盘   
三个人数版本从画像的 cast 派生。全员版是 MC 加全部 LI，主 CP 版是 MC 加第一个 LI，单人版只有 MC。LI 只有一个时全员版和主 CP 版相同，只跑一次。 
数据契约 
封面 skill 会拿到两样东西，作品画像和参考图清单。字段如下，都是站内 run 记录里的 JSON，也能从简报 markdown 里读到。 
作品画像 profile 
字段    取值    
work_title    作品名，来自 book.json 或文件夹名   
genre    最多 2 个，取自 TMDb 的 17 个电影题材，action、adventure、animation、comedy、crime、drama、family、fantasy、history、horror、music、mystery、romance、science-fiction、thriller、war、western   
mood    primary 与 secondary 各 1 个且不同，取 romantic、sensual、forbidden、dark、tender、epic、melancholy、playful、mysterious、defiant、energetic、other。注意海报侧几乎打不出 forbidden，打分时视同 sensual、dark、romantic   
era    contemporary、period、medieval-fantasy、futuristic、timeless   
setting    urban、interior、palace、forest、mountain-snow、water、beach、desert、sky-space、battlefield-ruins、rural、road、outdoor、stage-arena、void、other   
motifs    6 到 14 个开放名词，小写英文单数，按重要度排序，比如 phoenix、golden collar、log cabin、red ribbon   
subject    一句英文海报概念，18 词以内，说谁在画面里做什么   
cast    数组，每项 name、role（mc 或 li，mc 只有一个）、gender（female、male、unknown）、age_band（child、youth、adult、mature）   
inference_notes    哪些字段是推断的，字符串数组   
海报标签 tags 
每张候选海报都带这份标签，和画像是同一套词。figures 是 count、female、male、unknown 四个整数，crowd 是布尔，age、era、setting、mood 与画像同词表，motifs 是 3 到 8 个开放名词，subject 是一句英文主体描述，genre_guess 最多 2 个题材。 
匹配结果 results 
results.variants 是数组，每项对应一个人数版本。 
字段    内容    
name    full、couple、solo   
variant    figures 四个整数、age、members 人名数组   
relax_note    硬过滤放宽到了哪一层，未放宽、人数放宽 ±1、放弃题材过滤、两者都放   
candidates    最多 24 张，按分数降序。每张有 path、title、year、score、parts 分项、cos 余弦、relax、tags   
picks    5 项，index 是 candidates 里的序号从 1 起，reason 是中文理由   
海报图片不存在本站。缩略图地址是 POSTER_SITE_BASE 加 /_preview/ 加 path，原图地址是 POSTER_SITE_BASE 加 / 加 path，POSTER_SITE_BASE 目前是 https://impawards-posters-archive.1992155807.workers.dev 。path 形如 impawards-posters-2024/2024/10/Conclave.jpg，要做 URL 编码。这个站点会拒绝 Python 默认 UA，并且并发超过 18 路会重置连接，程序化取图要带自定义 UA 并限流。 
怎么把结果交给封面 skill 
三条路，看 skill 跑在哪。 
人手拿。结果页右上角「下载简报」得到 markdown，含画像 JSON、三个版本的终选 5 张（标题、年份、规则分、原图链接、理由）和全部候选表。文件名是作品名加 poster-brief.md。  
调接口。登录态下 GET /api/runs/{id} 返回完整 run，含 profile_final 和 results；GET /api/runs/{id}/brief 返回简报 markdown。会话是 Cookie poster_session，目前没有 API token，skill 要自动调的话得加一个服务端 token，这是一个待办。  
直接读库。Railway Postgres 的 runs 表，profile_final 和 results 都是 JSONB，posters 表存 4538 张的 tags 和 embedding。  
页面顺序是先作品画像再海报清单，画像可以在结果页改了重跑，重跑会生成新 run 并记录 parent_run_id。封面 skill 应该认 profile_final，不认 profile_auto。 
已经定下的边界 
这些是 wangbo 逐条拍板的，规划封面 skill 时不要推翻。 
参考海报只借鉴题材气质、情绪、人数和画面元素。构图、配色、字体、绘制风格都不打标也不借鉴，绘制风格由 Lunaverse 自己的角色立绘资产决定。  
海报图片一律不下载不存储，缩略图只在内存里过一下。封面 skill 如果要把参考图喂给生图模型，用原图 URL 传引用，不要落盘。  
成员上传的源文件不存库，抽完画像即清空。长期留的只有文件清单的路径、大小、sha256，加画像、结果、费用。  
模型全部走 Mob AI 网关 https://ai.mob-ai.cn/api ，不再用豆包，不用 Claude API 直连。  
封面最终用 GPT Image 2 生成。这是 wangbo 的原话，出图管线由封面 skill 负责。  
人物结构只匹配人数和男女配比，人物怎么摆属于构图，不在匹配范围。  
网关里可用的生图能力 
2026-09-13 从 https://ai.mob-ai.cn/integration.html 核对，封面 skill 大概率用得上。 
模型    要点    
image-gpt-2.5    POST /v1/generations，genType 取 sunburst 精修或 flare 快出，最多 16 张 references，支持 maskUrl，分辨率 1K、2K、4K，n 取 1 到 4，quality 到 xhigh 和 max，aspectRatio 控制比例。文档注明没有配置单价，usage 里拿不到美元费用   
image-gpt    普通生成最多 9 张 references。带 references 加 background transparent 时自动走官方通道，最多 16 张，出透明 PNG，适合抠立绘   
image-seedream-pro    最多 10 张 references，支持 bbox 局部替换，size 可指定精确宽高   
image-grok    最多 3 张 references，1K 0.05 美元、2K 0.07 美元，每张参考图加 0.01 美元   
references 只收 HTTP(S) URL，这和「不落盘」的边界正好配得上，海报原图链接可以直接传。每个模型都支持 mode async，轮询用同一模型加 input.taskId。结果存在网关的私有 R2，返回 Worker 地址。 
封面 skill 要回答的问题 
这些是本站没有决定、留给封面 skill 的，建议让 GPT 逐条给方案。 
三个人数版本用哪个出封面。App 里封面是否固定单人或双人，还是按作品 LI 数量选。  
参考图怎么进提示词。5 张原图 URL 全部当 references，还是只取第一张，以及提示词里怎么写「只借气质和元素，不抄构图配色」。  
角色立绘怎么进来。lunaverse-ide 的 character-portrait-v2 skill 出的立绘在哪，是否先用 image-gpt 透明模式抠出来再作为 references，参考目录 ~/Downloads/lunaverse-ide/agents/asset/skills/character-portrait-v2。  
封面上要不要放书名和文字，字体谁定，还是留白后期加。  
比例与尺寸。App 封面的实际展示比例和最小分辨率，决定 aspectRatio 和 resolution。  
成品存哪。网关返回的 R2 地址是否直接进 assets-produce 的 mapping.json，还是复制到团队自己的桶。  
放在哪里跑。做成 lunaverse-ide 的 agent skill，还是做成本站的第六步。前者与现有资产管线一致，后者用户点一下就能出图但要加服务端 token 和任务队列。  
费用与配额。一次出几张、每人每天几次，image-gpt-2.5 拿不到美元费用怎么记账。  
遗留与已知的坑 
题材是模型看图猜的，没接 TMDb。要接的话申请一个免费 API key 放进 .env，只改查询侧，不用重新打标。  
同一场景最多 12 张的候选多样性上限还没实现。  
情绪词表缺一个 comic 类的词，衣着类物件在当代题材里偏噪声，下版词表可以调。  
medieval-fantasy 海报全库只有 39 张，奇幻作品的候选主要靠 period 和 timeless 补。四人以上海报 270 多张，四人作品不缺候选。  
网关会拦 Node 和 Python 默认 UA，请求要带浏览器 UA。终选一次最多 16 张图，24 张会 502。  
本地 wiki 仓库 mob-wiki 有几笔关于本站的提交推不上去，本机 gh 账号 LinghuC2333 没有推送权限。  
自定义域名没挂。要上 mob-ai.cn 子域名，需要在 Cloudflare 加一条 CNAME 后用 railway domain 绑定，再改 APP_ORIGIN 和飞书回调。  
相关资料 
设计说明 docs/superpowers/specs/2026-09-09-poster-ref-site-design.md，实施计划 docs/superpowers/plans/2026-09-09-poster-ref-site-plan.md，都在仓库里。  
打标与匹配算法方案（飞书）https://xcn662409uz0.feishu.cn/docx/UVSDdJCwJoxy0WxiupFch914neb ，含词表、模型对比、全库分布、打分表。  
wiki 页 concepts/poster-reference-matching-tagging 与 entities/lunaverse-poster-ref-site，仓库 cdotlock/mob-wiki。  
Python 原型 ~/Downloads/Lunaverse AppStore Promotion/poster-ref，含全量标签、向量、两部样本的匹配页面。  
网关接入文档 https://ai.mob-ai.cn/integration.html ，key 的名字是 MOB_AI_KEY，值在 Railway 变量和本地 .env.local。  
