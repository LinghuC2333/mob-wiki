---
title: Lunaverse 新用户流失埋点需求（飞书原文导出）
source: https://xcn662409uz0.feishu.cn/docx/H2kodXaU2oNL1NxDRY1c4HhsnBe
exported: 2026-11-02
---

<title>Lunaverse 新用户流失埋点需求</title>

| 项目 | 内容 |
|-|-|
| 版本 | v0.1，待评审 |
| 日期 | 2026-11-02 |
| 负责人 | 产品 wangbo，客户端 [待填]，服务端 [待填]，数据 [待填] |
| 适用范围 | iOS、Android，线上分支 xcode-cloud-preview |
| 相关文档 | [投放与新用户数据报表](https://xcn662409uz0.feishu.cn/docx/DqAQdYNOyoJFKQxdFzscFwImnmf)、[新用户引导 PRD](https://xcn662409uz0.feishu.cn/docx/XawTdDIipooCpxxQODacMLg6n3e) |

# 要解决的问题

买量进来的新用户大多在开场就走了，但我们说不清走在视频第几秒、为什么走，也拆不出走的人是什么性别和年龄。这份需求要让下面四个问题都能从 Metabase 直接查到。

- 用户具体从哪里退出，精确到作品、集、步骤和视频第几秒。
- 退出时有没有技术原因，比如加载慢、卡顿、报错，以及有没有试图跳过。
- 退出的人和留下的人，性别和年龄分布有什么差别。
- 玩上第一集、玩完第一集、玩完 3 集的用户，分别是什么性别和年龄。

# 现在的数据能到哪一步

数据来自 Metabase 的 events_production，美东 10-30 到 11-01 的新用户，样本 3 天，数字用来定方向。

| 作品（tutor 卡片） | 看到开场视频 | 走到下一步 | 通过率 | 走完这一步的中位时长 |
|-|-|-|-|-|
| Where the Wild Keeps Us（Romance） | 40 | 7 | 18% | 78 秒 |
| The Crown That Answers（Royalty） | 28 | 9 | 32% | 188 秒 |
| The Bond She Defines（Alpha） | 24 | 6 | 25% | 203 秒 |
| The Price in Her Veins（Romantasy） | 15 | 2 | 13% | 81 秒 |

- 进了作品、没走到属性分配的 67 人里，62 人最后一条 game_progress 停在 0002_video。退出位置已经能定位到这一步。
- 没走到下一步的 83 人，从视频开始到第一次切后台，不到 10 秒的 8 人，10 到 29 秒的 38 人，30 到 59 秒的 24 人，1 分钟以上的 13 人。这是用切后台时间估的，切后台也可能只是暂时离开。
- 客户端 VideoWnd 里长按 0.5 秒可以跳过视频，但跳过没有上报，也不知道玩家是否知道这个操作。

| 想知道的事 | 现状 | 缺口 |
|-|-|-|
| 退出在哪一步 | game_progress 有 content_id、episode_id、step_key、step_kind | 进度只在步骤切换时记，视频内部没有记录 |
| 视频第几秒走 | 只能用 lifecycle_changed 切后台时间估 | 没有播放位置和视频总长 |
| 是否跳过 | VideoWnd 有 skipped，只写本地日志 | 没有事件 |
| 技术原因 | 代码里定义了 api_request_failed、api_request_slow，10-28 以来 events_production 查不到；RuntimeMonitoring 只有面包屑 | 素材加载、视频卡顿、播放报错都没有事件 |
| 走的方式 | game_exit 只有主动退出作品才记，10-30 以来只有 14 次 | 切后台不回来和杀进程分不开 |
| 属性和选项曝光 | 代码里定义了 attribute_allocation_presented、choice_presented、choice_clicked，10-28 以来查不到 | 要确认是没发还是没入库 |
| 性别和年龄 | 后端用户资料有 birthDate、gender；onboarding\_\* 走 trackFirebaseEvent，只进 Firebase | 分析库没有，users 表也没有这两列 |
| 看完开场 | 1.0.12 起 episode_started 进作品就触发，intro_completed 只有少数人有 | 这两个事件的含义变了，没法和之前比 |
| 第几集 | episode_started、episode_completed 只有 episode_id | 没有集序号，算不出第一集和完整 3 集 |

# 范围

| 本期做 | 本期不做 |
|-|-|
| 同步性别和年龄段到分析库 | App 内退出问卷，等埋点数据出来再定 |
| 新增 6 个事件，补齐已定义事件的上报 | 改开场视频内容或加跳过提示，这是看到数据后的产品决定 |
| 给 episode 事件补集序号，理清开场相关事件的含义 | 接 MMP 或广告来源归因，单独评估 |
| 在 Metabase 能出下面的报表 | 逐次点击全量上报，只做累计计数 |

# 需求一 同步性别和年龄段

后端把 tutor 收集的资料同步到 BigQuery 的 moonshort_analytics.users，或单独一张按 user_id 关联的表。不需要改客户端。

| 字段 | 取值 | 说明 |
|-|-|-|
| gender | female、male、non_binary、not_say，没填为空 | 用户在 tutor 第 2 步选的 |
| birth_year | 整数，没填为空 | 只同步年份，不同步完整生日 |
| age_band | 18-24、25-34、35-44、45-54、55-64、65+，18 岁以下单独一档，没填为空 | 按同步时的年份算，报表优先用这一列 |
| onboarding_completed_at | 时间，没完成为空 | 用来区分没走完 tutor 的人 |
| onboarding_genre | royalty、romance、alpha、romantasy，没选为空 | 用户第 3 步选的卡片 |

验收标准。tutor 上线以来注册的账号全部回填；随机抽 20 个账号，和后端资料一致；同步延迟不超过 1 天。分析表里不新增完整生日。

另外把 onboarding_view、onboarding_birthday_submit、onboarding_gender_submit、onboarding_genre_click 这四个事件也送进 events_production，不然 tutor 哪一步掉人在 Metabase 里看不到。这几个事件的 birth_year 和 gender 参数保留。

# 需求二 新增事件

新事件走 BusinessAnalytics.track，这样才会进 events_production。要同步加进 BusinessEventName，新参数加进 ANALYTICS_PRIORITY_PARAMS，单个事件不超过 24 个参数。共同参数都带 content_id、episode_id、episode_index、step_key、step_kind。

| 事件 | 什么时候发 | 额外参数 | 回答什么 |
|-|-|-|-|
| story_video_started | 视频真正开始播第一帧 | video_duration_ms，load_ms（从进入步骤到第一帧） | 视频多长，等了多久 |
| story_video_progress | 播到 25%、50%、75%，每个节点每次播放只发一次 | progress_pct，position_ms | 视频哪一段掉人 |
| story_video_ended | 视频结束 | reason（ended、skipped、error），position_ms，video_duration_ms，tap_count，skip_hold_count，stall_count，stall_ms | 看完还是跳过，点了几次，卡了多久 |
| story_left | 作品内切后台，或主动退出作品 | exit_type（background、exit_button、其他），is_video，position_ms，foreground_ms，tap_count，stall_count，network_type | 走的时候在哪、怎么走、当时的网络 |
| story_asset_load | 素材加载失败，或耗时超过 3 秒 | asset_type（video、image、audio、bundle），duration_ms，outcome，error_code | 是不是加载的问题 |
| client_error | 播放报错或脚本异常 | error_type，error_code，是否致命（fatal） | 是不是出错了 |

tap_count 记这次视频播放期间点屏幕的次数，skip_hold_count 记按住想跳过但没跳成的次数。这两个是累计数，放在结束和离开事件里，不要每点一次发一条。

杀进程时事件可能发不出去。切后台时先把 story_left 写进待发队列，下次打开补发。下次打开时能判断上次没有正常结束的，再补一条 story_left，exit_type 记 killed_or_crashed。

## 要补的现有事件

| 事件 | 要做的事 |
|-|-|
| episode_started、episode_completed | 加 episode_index，第一集是 1 |
| attribute_allocation_presented、choice_presented、choice_clicked、api_request_failed、api_request_slow | 确认客户端是否真的发了，服务端是否入库，修到 events_production 能查到 |
| intro_completed、episode_started | 说明 1.0.12 改了什么、现在什么时候触发，写进事件说明 |
| game_presentation_ready | 10-28 以来有 37 个进了作品的新用户没有这条，确认是没加载出来还是没上报 |

# 报表怎么看

数据到位后，在 Metabase 出下面三张表。阶段定义是我的建议，评审时确认。

| 阶段 | 定义 | 要拆的维度 |
|-|-|-|
| 进入作品 | game_entry | 性别、年龄段、tutor 卡片 |
| 看完开场视频 | 第一个视频 story_video_ended，reason 是 ended 或 skipped | 同上，再分看完和跳过 |
| 玩上第一集 | episode_index 为 1 的集里，第一次完成属性分配或选项 | 同上 |
| 玩完第一集 | episode_completed，episode_index 为 1 | 同上 |
| 玩完 3 集 | 同一部作品 episode_index 为 1、2、3 的 episode_completed 都有 | 同上 |

- 退出分布表，按作品、集、步骤和视频进度分档（0 到 25%、25 到 50%、50 到 75%、75% 以上）看人数和占比。
- 退出原因线索表，把退出的人分成有报错、加载超过 3 秒、卡顿、试图跳过、以上都没有五组。这一组数据只能说明可能的原因，真正为什么不想看，还要靠用户访谈。
- 人群表，上面五个阶段各自的性别、年龄段人数和占比，没填资料的人单列一组。单格少于 30 人时只看人数，不看百分比。

# 验收

- 在 iOS 和安卓各走一遍这几条路径，事件和参数都能在 events_production 查到。看完视频；看到 30% 切后台；长按跳过；看到一半杀进程后重开；断网进入作品。
- 长按跳过后，story_video_ended 的 reason 是 skipped，position_ms 等于实际位置。
- 同一次播放里 25%、50%、75% 各只有一条，切后台再回来不重复发。
- 杀进程后下次打开，能查到补发的 story_left。
- 埋点失败不影响播放和进度，事件量增加后 App 启动和视频播放没有可感知的变慢。

# 隐私

分析表只放年份和年龄段，不放完整生日。事件里不带邮箱、用户输入的文字和设备标识。18 岁以下的用户只出汇总数，最低年龄门槛和未达年龄用户怎么处理，tutor PRD 里还开着，这份需求不处理。隐私政策目前是否已写明收集生日和性别的用途，需要法务确认。

# 开放问题

| 问题 | 建议 | 谁确认 |
|-|-|-|
| Firebase 有没有导出到 BigQuery。有的话 tutor 事件和历史数据能直接查 | 数据先看一下，有就不用重复补历史 | 数据 [待填] |
| 线上开场视频有没有跳过提示，玩家怎么知道长按能跳 | 产品和客户端看一下现状 | 产品、客户端 |
| 这批改动跟哪个版本发，老版本的用户怎么算 | 报表按 app_version 筛，只看新版本 | 客户端、数据 |
| 「玩上第一集」按第一次互动算是否合适 | 评审时定 | 产品、数据 |
