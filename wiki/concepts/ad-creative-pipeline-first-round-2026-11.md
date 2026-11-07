---
title: 按作品投放素材制作流程（第一轮测试，2026-11-03）
tags: [ad, video, pipeline]
sources: [raw/2026-11-07-mixreel-0.0.1-and-script-method.md]
created: 2026-11-04
updated: 2026-11-07
---

# 按作品投放素材制作流程（第一轮测试）

4 条片子（灵媒、吻完说错了、他说我按不倒他、全族都让路）2026-11-04 由王博定稿，还没投放。配乐暂用商业歌曲选段，王博和代理商确认过可以投。

- 测试报告（含成片、耗时、花费）：https://xcn662409uz0.feishu.cn/docx/RDDRdy1t1ozX4BxVK3scUr4pnig
- 问题清单多维表格（72 条，含根因和证据）：https://xcn662409uz0.feishu.cn/base/UJLHb9MRaabnh4s7oxucoPqHnlg
- 本地素材：`Lunaverse AppStore Promotion/投放素材/第一轮按作品/`，渲染器在同项目 `tools/lunaverse-ui/`

## 跑通的做法

1. **脚本**写清每镜是现实还是作品，掷骰前必须演选项或 Remix。完整 Remix 加掷骰约 8 秒，要提前留时长。
2. **选歌切点**用 Gemini 听 20 到 30 秒片段，放完最后一个 8 拍，全片音量恒定，只在片尾淡出。
3. **人物图**用 Midjourney 的 omni（`--oref`）喂线上立绘上半身，权重约 `--ow 700 --sw 250`。MJ 只用到这一步。
4. **关键帧**把旧帧模糊成色块当姿势稿，Ideogram 配人物图出帧，提示词写清发型和领子。主图是成品图时 Ideogram 基本原样返回且照收费。GPT 只修局部小毛病，不用它重画或强修脸。
5. **视频**用 MiniMax H3，提示词开头写画风和动画感，对手也要写表演。网关同时接 4 个，超出返回 429。对视频接口带 dryRun 会真实扣费。
6. **真实界面**用 `render_dice`、`render_remix`、`render_choice`，参数取客户端 xcode-cloud-preview 分支。游戏内台词都用客户端对白框。
7. **合成**时作品和现实镜头降到每秒 12 张，界面保持每秒 30 张。
8. **片尾**接官方 outro（原片在《Lunaverse 四类宣传视频｜外包制作总说明》），加速到 3 秒并静音，配乐放进 outro 里淡出。右上角放透明字标，宽 260 像素，不盖产品界面。

## 还没定的事

- 两条片里 Tristan 长得不一样，要不要统一。
- 片子都到 27 秒以上，超过原来 15 到 20 秒的规矩，等投放数据再定。
- 网关几个问题待工程师处理：视频 dryRun 不报错、Ideogram 原样返回照收费、GPT 单价和余额查不到。

公司对素材的完整要求在项目 `brand/素材要求.md`。模型特性 11-05 已写进 MixReel。这一轮暴露的返工多半出在脚本，11-06 起写脚本的方法整体改了，见 [[concepts/mixreel-script-method-2026-11]]；创意和返工记在 [[concepts/ad-idea-ledger]]。
