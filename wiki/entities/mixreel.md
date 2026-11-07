---
title: MixReel
tags: [video, ad, skill, oss]
sources: [raw/2026-11-07-mixreel-0.0.1-and-script-method.md]
created: 2026-11-07
updated: 2026-11-07
---

MixReel 是一套写广告脚本、规划和生成视频、审片的 agent 技能，分团队版和公开版两个仓库，两边版本号一致，2026-11-07 发布 0.0.1。写脚本的方法见 [[concepts/mixreel-script-method-2026-11]]，记创意的账本见 [[concepts/ad-idea-ledger]]。

## 仓库

| 仓库 | 用途 | 本机远端名 |
|-|-|-|
| MobAI-Inc/MixReel-team | 团队私有版，Buzz 和内部同事用，含 mixreel-lunaverse 技能、内部报告、内部安装脚本 | mixteam |
| MobAI-Inc/MixReel | 公开版，只做产品广告，不加剧情模式 | team |
| LinghuC2333/MixReel | 个人仓库，10-30 后不再用 | origin |

本机远端名和仓库对不上，推之前按 URL 认。两边没有共同 git 历史，团队版提交不能直接推公开版。本机 ~/Downloads/mixreel 是团队版，~/Downloads/mixreel-public-sync 是它的 worktree，用来改公开版，两边共用标签，推标签要写明 refspec。

## 同步规矩（钟文鼎 2026-11-07 定，写在团队版 AGENTS.md）

- 不许用脚本导出。团队版 skills/ 下除 mixreel-lunaverse 外的改动合并后，一个 agent 读改动和公开版对应文件，按公开版自己的写法手工改，逐行查内部信息，跑测试后提一个 PR。
- 另开一个不带上下文的 agent 审代码质量和泄露，没问题才合并。
- 两边版本号一致，从 0.0.1 起依次递加，团队版记在 versions.json，公开版记在 CHANGELOG.md，两边打同名标签。
- 公开版 main 只走 PR，CI 的 test 必过，至少一人批准，禁强推禁删，外部贡献者的 CI 要先批准才跑。
- 永远不进公开版的有 mixreel-lunaverse、内部报告、内部安装脚本、versions.json、产品名客户名人名、各种路径、内部域名和网关、设计稿链接、密钥。

## 0.0.1

公开版手工对齐团队版的通用技能，不带 mixreel-lunaverse、剧情改编（story.md）和内部文档，过两轮盲审后压成单个提交强推为 main（9ebc8fa，署名 MobAI Inc），删旧分支，打 v0.0.1。团队版 v0.0.1 指向 025709f。H3 六段式提示词格式最早来自 Lunaverse IDE 项目的内部技能，王博确认是自己总结、不含隐私，公开版保留。

待钟文鼎收尾的有联系邮箱、README 里 "truly open source" 的说法，以及二审列出、计划放进 0.0.2 的 7 条质量问题。
