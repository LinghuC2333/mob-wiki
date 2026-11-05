# Find Your LI 内容改版技术方案

| 项目 | 内容 |
| --- | --- |
| 文档版本 | v1.0 |
| 撰写日期 | 2026-08-27 |
| 关联 PRD | `prd-find-your-li-content-v1.0.md` |
| 状态 | 待评审 |

## 一、现有架构与约束

正式入口是 `result-page-demo/demo.html`，纯静态页面，设计目标是**直接双击打开（file:// 协议）也能完整运行**。这带来两条硬约束：

1. 不能用 `fetch` 加载数据，所有数据通过 `<script>` 标签挂 `window` 全局（现状：`CHARACTER_POOL`、`QUESTION_SIGNALS`、`CHARACTER_IMAGE_DATA`）。
2. 分享卡的立绘在 file:// 下走 `data/character-image-data/<id>.js` 的 base64 懒加载，此机制不动。

现有代码分层：

| 文件 | 职责 |
| --- | --- |
| `demo.js` | 视图状态机 + 渲染 + 分享卡 canvas。**题面文本在这里手抄了一份**（与 questions.json 重复，是漂移隐患） |
| `data/questions.json` | 题库事实源（30 题 + 信号），当前只有人读，页面不加载 |
| `data/question-signals.js` | 信号的 JS global 版（手工同步，第二处重复） |
| `data/character-pool.js` | 100 角色数据，由 `scripts/build-character-pool.mjs` 生成 |
| `matcher-browser.js` | 匹配 + `describeResult` 文案模板 |
| `src/matching/matcher.mjs` + 测试 | Node 版 matcher，与 browser 版是**两份手工同步的实现** |

`app/page.tsx`（Next 壳）是早期 hero 静态稿，本期不动、不作为交付物。

## 二、总体设计

原则：**内容与代码分离、生成物与事实源分离、一切生成可重复**。

```text
data/questions.json          事实源（题库 v2，人工维护）
        │ scripts/build-questions.mjs
        ▼
data/questions.js            生成物：window.QUIZ_QUESTIONS（题面）
data/question-signals.js     生成物：window.QUESTION_SIGNALS（信号）

data/character-content.js    事实源（角色内容库，人工维护，JS global）
data/character-pool.js       现状保留（本期只修 power 英文映射所需数据不动，映射放代码层）

scripts/compute-rarity.mjs   离线蒙特卡洛 → data/rarity.js（window.CHARACTER_RARITY）
scripts/validate-content.mjs 全量校验（覆盖率 / 长度 / CJK / 剧透关键词黑名单）
```

`demo.js` 删除手抄题库，改读 `window.QUIZ_QUESTIONS`。三处重复归一为 questions.json 单源。

## 三、模块设计

### 3.1 题库构建（build-questions.mjs）

- 输入 `data/questions.json`，输出两个生成物文件，文件头注明 generated 与生成时间。
- 校验：每题恰 4 选项、每选项 1–2 个信号、信号语法合法（复用 matcher 的 parseSignal 正则）、维度 +/- 配平比 ≤ 2:1（PRD 5.1 原则 3），不达标直接报错退出，防止手改 json 后带病生成。

### 3.2 角色内容库（character-content.js）

```js
window.CHARACTER_CONTENT = {
  "lucifer": {
    hook: "...",        // ≤140 chars
    quote: "...",       // ≤120 chars，原创 in-voice
    fallMoment: "...",  // ≤200 chars
    friction: "...",    // ≤200 chars
    verdict: "...",     // ≤200 chars
    whisper: "..."      // ≤90 chars，分享卡用
  },
  // … 100 entries, key 与 CHARACTER_POOL[i].id 严格对应
};
```

稳妥性设计：`describeResult` 读内容时全部走 `content?.field ?? 模板兜底`。任何角色缺条目、缺字段，页面不崩、退回模板句。校验脚本负责在构建期把缺口暴露出来，运行期只兜底不报错。

### 3.3 describeResult v2（matcher-browser.js）

改动范围收敛在文案组装层，**打分、排序、winner 选取逻辑不动**（PRD 非目标 2）。

1. 新增 `POWER_LABELS` 映射：`权力悬殊 → a steep power gap` 等 5 个值（含 undefined 兜底），消灭 CJK 输出。
2. `describeResult(profile, scored, extras)` 增加第三参 `{ runnerUps, rarity, content }`，由 `completeQuiz()` 注入，保持函数纯净可测。
3. 返回对象新增字段：`quote`、`rarity`（展示文案已格式化）、`runnerUps[]`（name/work/image/percent）、`roast`、`patternBars[]`（九维中有 evidence 的维度：label、用户端位置、角色端位置，均归一到 0–100）。
4. runner-up 贴合度百分比 = `Math.round(result.total * 100)`，与 winner 的分数同口径，不另造数。
5. Roast 引擎：纯函数 `pickRoast(profile, answers)`，规则表驱动（`[predicate, template]` 数组，顺序即优先级），全部规则不命中时用最极端维度的兜底模板。规则与模板放 matcher-browser.js 顶部常量区，便于文案迭代。

### 3.4 稀有度预计算（compute-rarity.mjs）

- Node 脚本，import `src/matching/matcher.mjs`？**不行**——browser 版才是 demo 实际用的实现。方案：脚本用 `node:vm` 加载 `matcher-browser.js` + `character-pool.js` + 生成后的 `question-signals.js`，保证模拟与线上同一份代码，杜绝双实现漂移。
- 固定种子的 mulberry32 PRNG，10 万组均匀随机作答，统计每个角色的 winner 次数 → `data/rarity.js` 写 `window.CHARACTER_RARITY = { id: 出现率 }`。
- 同时输出趋同度报告（单角色最大占比、top10 合计、零中签角色名单）到 stdout，作为 PRD 反向指标的验收数据。零中签角色的稀有度文案统一「fewer than 1 in 100」。

### 3.5 结果页新模块（demo.html / demo.css / demo.js）

- 新增 DOM：quote 块、稀有度徽章、pattern 条形图容器、roast 卡、runner-up 列表。全部纯 CSS，无新依赖。
- 条形图用 div 宽度百分比实现，双标记（用户端▲ / 角色端●）落在同一轨道上；禁用 canvas，保证可访问性和暗色兼容成本最低。
- 分享卡 canvas：增加稀有度行 + whisper 行，坐标在现有 1080×1350 版式内微调；文本继续走 `fitCanvasText` 防溢出。
- runner-up 头像沿用 `public/characters/*.webp`；file:// 下 canvas 不涉及 runner-up 图（只进页面 DOM），无跨域污染问题。

### 3.6 校验脚本（validate-content.mjs）

构建期一键校验，CI 语义（退出码非 0 即失败）：

1. 内容覆盖率：CHARACTER_POOL 每个 id 在 CHARACTER_CONTENT 有全部 6 字段。
2. 长度上限：按 PRD 5.2 表。
3. CJK 扫描：全量角色跑 describeResult（模拟一组答案），输出含 `[一-鿿]` 即失败。
4. 剧透黑名单：内容字段扫描 `dies|death|betray|ending|turns out|revealed|secretly the` 等词表，命中人工复核（warning 级）。
5. 互换测试抽样辅助：随机抽 5 对角色打印 quote 对照，供人工判断声线可区分性。

## 四、实施顺序与回滚

| 步骤 | 改动 | 风险控制 |
| --- | --- | --- |
| 1 | build-questions.mjs + 题库 v2 json → 生成物接入 demo.js | 旧 30 题 json 存档为 questions-v0.4.json，可一键回退 |
| 2 | POWER_LABELS 修复 + fandom 文案重写 | 独立小改动，先行合入 |
| 3 | character-content.js 分批产出 + validate 脚本 | 兜底机制保证半成品状态页面可用 |
| 4 | describeResult v2 + demo.js/HTML/CSS 新模块 | content 缺失时全部退回模板，页面永不白屏 |
| 5 | compute-rarity.mjs 跑数 + rarity.js 接入 | rarity 数据缺失时徽章整块隐藏 |
| 6 | 分享卡 v2 | canvas 绘制失败已有 try/catch toast 兜底 |

每步完成后用浏览器过一遍完整流程（落地 → 答题 → 结果 → 下载卡片），第 6 步后跑全部校验脚本 + `src/matching/matcher.test.mjs` 回归。

## 五、明确不做

- 不合并 src/matching 与 matcher-browser 两份实现（超出本期范围，用 3.4 的 vm 加载方案规避漂移风险，遗留问题记入 README 下一步）。
- 不引入构建工具链/打包器，保持双击可用。
- 不动匹配权重与 winner 轮换逻辑。
