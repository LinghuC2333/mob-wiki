---
title: Lunaverse Tutor v1.1 版本优化
tags: [onboarding, tutor, prd, moons, economy, analytics]
sources: [raw/2026-11-04-lunaverse-tutor-v1-1-prd.md]
created: 2026-11-04
updated: 2026-11-04
---

这一页记录新用户引导 Tutor v1.1 的需求，目前是草稿，待评审。这版用 Moons 把新用户带完 tutor，同时收窄登录方式，并把 Moons 的面额乘 100。其中 100 倍方案还没有最终确认，钟文鼎提议过 200 倍，后面定了要回来更新这一页。

飞书原文 https://xcn662409uz0.feishu.cn/docx/XEUAddRzIoyOH1xj14yc5RrVnDc

设计稿 https://www.figma.com/design/XeuFxRu06jkMunxq9nDM5A/LUNAVERSE-APP?node-id=6919-1113 ，欢迎页主视觉在 node 6937-1169

上一版 PRD https://xcn662409uz0.feishu.cn/docx/XawTdDIipooCpxxQODacMLg6n3e

## 这版改了什么

| 模块 | 线上现状（1.0.12） | v1.1 |
|---|---|---|
| 登录方式 | Apple、Google、邮箱，邮箱页里有游客入口 | Google、Facebook、Apple，删除邮箱和游客入口 |
| 登录气泡 | 无 | Sign up and get 2,000 moons! |
| loading | 现有 GameLoading | 新样式，启动时带 tips，登录后不带 |
| 欢迎页 | 无 | 新增，主视觉配奖励说明，主按钮 Continue |
| 首页弹窗 | Welcome，文案写 25 Moons | Claim 2,000 Moons，只有完成 tutor 的用户才弹 |
| Discord 弹窗 | 无 | 领取动画播完后弹，点按钮即送 1,000 Moons |
| 注册奖励 | 注册时发 30 Moons | 注册不发，完成 tutor 后领取 |
| Moons 面额 | 1 美元约 10 到 18 个 | 统一乘 100，后端和老数据一起改（待确认） |

用户流程是启动 loading、登录、登录后 loading、欢迎页、生日、性别、选 genre、作品、回到首页、Claim 弹窗、领取动画、Discord 弹窗。已完成 tutor 的用户直接进首页。

## 已定的决策

- 保留 Facebook 登录。
- 气泡负责把人带进来，文案写注册就送，实际领取必须完成 tutor，这是有意设计。
- Discord 只看点击，点主按钮就发 Moons，不校验是否真的加入服务器。
- 隐私说明不放欢迎页，入口留在生日页。
- 老用户不补发 Moons，账号资料页的绑定邮箱入口保留。
- 注销后不能重复领，后端按第三方账号去重。
- 关注社媒、补填邮箱等任务归入后续的 task list，Discord 弹窗关闭后的长期入口也放到那时再定。

## 为什么可以删邮箱和游客

美东 10-28 投放以来注册、能查到登录方式的 273 人里，Apple 161 人（59.0%），Google 87 人（31.9%），游客 18 人（6.6%），邮箱 5 人（1.8%），Facebook 2 人（0.7%）。邮箱和游客合计 8.4%，这是删除后注册量损失的上限。这批人偏女性、偏年长，但样本太小，留存差异不能算数。

## Moons 数额和 100 倍方案（待确认）

tutor 送 2,000，Discord 送 1,000。新用户第一天最多拿到 3,600 个（2,000 + 1,000 + 签到 400 + 打完一集 200），等于现在的 36 乘 100，实际价值没有增加。

依据是现在没有人缺 Moons。10-28 以来注册的 279 人里只有 19 人花过，合计 236 个（现行单位），只占免费发放量的 2.5%，按流水推算没有人花光过。我们的奖励占最小包 100%，OOC 是 25%，Tipsy 是 43%。我们看剧情不要钱，Moons 花光也能接着玩。

选 100 倍是因为原价正好 1 美元 = 1,000 Moons，以后做按量扣费的聊天时方便算 token，乘完全是整数，也不用再通胀一次。Tipsy 按记忆长度算钱，1 美元只换 169 到 250 宝石，说明算 token 不需要 200 倍那么细。按量聊天的计划还没有找到方案，要和钟文鼎确认。

| 项目 | 现在 | 100 倍后 |
|---|---|---|
| 充值包 | 20 / 60 / 140 / 320 / 900 | 2,000 / 6,000 / 14,000 / 32,000 / 90,000 |
| 重投、直接通过、复活、回溯 | 2 / 4 / 8 / 12 | 200 / 400 / 800 / 1,200 |
| Bubble 礼物、首月、续费 | 20 / 50 / 100 | 2,000 / 5,000 / 10,000 |
| 七日签到 | 4、12、12、4、4、4、20 | 400、1,200、1,200、400、400、400、2,000 |
| 累计宝箱 | 20 / 30 / 40 / 80 | 2,000 / 3,000 / 4,000 / 8,000 |
| 打完一集、坏结局、完整结局 | 2 / 5 / 10 | 200 / 500 / 1,000 |

做法是直接改后端和老数据，只留一套数字。账号余额、Bubble 和礼物价格、所有常量、三个支付渠道的到账数量一次乘 100，历史记录不改，每个账号在 GemLedger 写一条转换记录。客户端把重投和直接通过价格写死了，后端也不能按版本区分，所以切换时所有 v1.1 之前的版本都要用 forceUpdate 强制升级。上线细节和验收见飞书原文的 C1 到 C8。

现行价格以 [[concepts/iap-sku-pricing]] 页首的校准说明为准，切换前不要按 100 倍的数字开发或对账。

## 埋点

新事件不绑定 Moons，也不绑定弹窗，以后的任务系统只需要加取值。

| 新事件 | 触发时机 | 参数 |
|---|---|---|
| surface_presented | 弹窗或页面真正展示 | page_id、view_id、source |
| surface_clicked | 点按钮 | page_id、view_id、element_id、target_type |
| reward_claim_attempt | 发起领取 | claim_kind、reward_id、operation_id |
| reward_claim_result | 领取返回 | claim_kind、reward_id、operation_id、outcome、error_code、reward_amount |

发放继续用 virtual_currency_granted，reason 增加 tutor、discord，和领取事件用 operation_id 串起来。签到的 check_in_claim_* 不改。

现有 Welcome 弹窗没有任何埋点。曝光不复用 content_view，因为它在 TikTok 里对应广告事件 ViewContent。余额不进客户端埋点，花光占比用后端流水 currency_ledger 计算，这张表现在不在 Metabase 连的 moonshort_analytics 里。

新事件要同时改客户端事件定义和 TikTok 映射、后端 native-events.ts 的事件枚举和参数白名单、BigQuery 的事件列表和参数列表。必须先发后端，否则带新事件的整批上报都会被拒。

## 上线前的基线

| 指标 | 当前值 |
|---|---|
| 注册转化率 | 85%（169/198） |
| 注册到进作品 | 90%（152/169） |
| 首集完播率 | 30%（45/152），v1.0 目标 50% |
| 次留 | 13%（28/214） |

漏斗统计美东 10-30 起的新用户，次留统计 10-28 到 11-01 注册的用户，数据截至美东 11-03。

## 还没定的事

- 是否确定 100 倍，这是最大的一项。
- 游客和邮箱老账号怎么办。切换时所有人都要升级到没有游客入口的版本，必须上线前定。
- 后端迁移的完整清单、停写时长和回滚方案。这次读的后端 main 最新提交是 10-22，线上要再核对。
- 第一部作品里余额为 0 时怎么接住用户，约 3% 的人会碰到。
- 隐私政策没有提到 tutor 收集的生日和性别。
- 去掉游客后是否符合苹果 5.1.1(v) 对强制登录的要求。
- 按量扣费聊天的计划和时间。

## 相关页面

- [[concepts/iap-sku-pricing]] — 现行充值商品和价格
- [[concepts/lunaverse-new-user-dropoff-tracking]] — 新用户流失埋点需求
- [[entities/lunaverse-client]] — 客户端
- [[entities/lunaverse-backend]] — 后端
