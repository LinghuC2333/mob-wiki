---
title: find-your-li-content-redesign-2026-08
tags: [find-your-li, romance-club, quiz, growth]
sources: [raw/find-your-li-content-prd-v1.md, raw/find-your-li-content-tech-design-v1.md]
created: 2026-08-27
updated: 2026-08-27
---

# Find Your LI 内容与结果页改版（2026-08 立项）

Find Your LI 是面向 Romance Club 玩家的 LI 匹配测试页（Lunaverse App Store 推广引流用），
代码在 `Lunaverse AppStore Promotion/what-li-is-made-for-you/result-page-demo/`，正式入口
`demo.html`（纯静态、file:// 可直接运行）。本页记录 2026-08-27 内容审查结论与 v1.0 改版方案。
完整文档：项目仓库 `docs/prd-find-your-li-content-v1.0.md` 与 `docs/tech-design-content-v1.0.md`
（副本在本库 `raw/find-your-li-content-prd-v1.md`、`raw/find-your-li-content-tech-design-v1.md`）。

## 审查结论（改版依据）

1. **结果页是模板填空**：全部分析文案来自 `matcher-browser.js` 里 9 段维度文案 + 5 段情感文案；
   fallMoment / verdict 100 角色同句；58 角色 gives=safe-base + 20 空数组 → 78% 用户看到同一张
   「WHAT HE GIVES YOU」卡。角色池无任何角色个性文本，是病根。
2. **题库信号失衡**：`stability+` 24 次 vs `stability-` 6 次、`warmth+` 15 vs 1、`openness+` 15 vs 5，
   多数用户画像趋同 → 分析文案趋同。外貌题 ×5、旅行计划 ×4、「TA 撒谎」×2，30 题 ≈ 18 题信息量。
3. **无社交货币**：无稀有度 / 百分比 / 候补对比 / 属性可视化 / 毒舌，分享动机缺失。
4. **已知 bug**：`relationship.power` 中文标签直接渲染进英文文案（powerLine）。

## v1.0 方案要点

- **题库 v2.0**：30 → 15 题（9 相处 + 2 外貌 + 2 雷区 + 2 生活图景）。硬规则：取舍型选项（不许四个
  都是安全好答案）、每题四选项四种声线、维度 +/- 配平比 ≤ 2:1、同场景全卷唯一、信号标注过
  「选这条的人真实想要什么」复核。
- **角色内容库**：100 角色 × 6 字段（hook/quote/fallMoment/friction/verdict/whisper），取材 fandom
  wiki（角色池存有 wikiUrl）+ 官方宣传物料。红线：无剧透（落地页承诺 NO STORY SPOILERS）、
  台词全部原创 in-voice 不逐字引用游戏文本。质检：互换测试 + 剧透词黑名单扫描。
- **结果页 v2**：接入角色专属文案；新增稀有度徽章（离线蒙特卡洛 10 万组固定种子预计算）、
  runner-up top3 对比、love pattern 双标记条形图、规则触发的 roast line。
- **技术关键决策**：questions.json 单源 + 脚本生成 JS global（消灭 demo.js 手抄题面的三处重复）；
  稀有度模拟用 `node:vm` 加载 `matcher-browser.js` 本体（demo 实际实现），规避 src/matching 双实现
  漂移；内容全部 `content?.field ?? 模板兜底`，半成品状态页面不崩；不动匹配权重与 winner 轮换。
- **验收指标**（离线可测）：单角色中签率 ≤5%、top10 合计 ≤30%、角色专属字段 0 重合、
  describeResult 输出 CJK 扫描为 0。

## 实施结果（2026-08-27 当天完成）

全部 P0/P1 交付：题库 v2.0（15 题）上线 demo；100 角色内容库（10 个并行 agent 读 fandom
wiki 产出，fandom 被 Cloudflare 拦 WebFetch 后改走 MediaWiki API）；角色池九维精修
100/100 唯一向量（原 37 人共享模板向量）+ visual 标签 15→≤5 瘦身；结果页 v2 全模块 +
分享卡 v2。验收：覆盖 100/100，max 4.58%，top10 31.8%（目标 30%，判定接受，见
test-run-002.md），CJK 0，旧 matcher 回归 5/5。过程中发现并修复：v0.4 题库 9 个死信号
（打在池中不存在的标签上）、roast 兜底不分方向 bug、pattern 条 0% 裁切、3 处角色池数据错
（luna wikiUrl 消歧 / christian-de-clare 作品归属 / sarah-vonnegut trope）。轮换带宽
0.045/12 → 0.07/20（分布平滑，非权重改动）。

**v1.2 增补（同日）**：稀有度改为真实数据——新增 `server/server.mjs`（无框架 Node，静态托管 +
`/api/track`（昵称/进度/完成/结果，session upsert）+ `/api/rarity`；Railway Postgres / 本地 JSON
双模）。完测 <100 显示「你是第 N 位」，≥100 显示真实占比，模拟降级为配平 QA 工具。部署目标
Railway（服务根目录 server/，挂 Postgres 插件即可）。匹配规则说明飞书文档：
https://xcn662409uz0.feishu.cn/docx/APTrdfun7osWQQxk1LkcVJpKnAg

**v1.3 增补（2026-08-28）**：20 人格实测闭环。确定性人格作答 → 真实埋点链路 → 5 组
agent 角色扮演评估（n=20，分享意愿 6.30）。全组一致结论：角色专属字段挣分、模板中段
（intro/理由/GIVES）掉分，另抓出负方向文案 bug（全克制作答被描述成 all-in love）。修复：
九维双向文案、理由三卡重构（含雷区对齐卡）、100 角色专属 GIVES/RECEIVES（4 新字段，
标题全池唯一）、题库 v2.2（3 处歧义）、roast 新组合规则。8 关键人格复测 +9，<5 分清零。
详见项目 docs/test-run-003.md。

## 关联

- 前置文档（项目仓库 docs/）：redesign-v0.1、matching-design-v0.1、question-bank-v0.1~v0.4
- 交付文档：question-bank-v0.5、test-run-002（验收记录）
- 旧项目数据：`/Users/wangbo/Downloads/增长裂变活动`（PostgreSQL `li_quiz`，353 RC 角色）
