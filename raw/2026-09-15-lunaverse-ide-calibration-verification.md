---
title: Lunaverse IDE 校准补充验证与收尾主线差异
created: 2026-09-15
source_repository: https://github.com/MobAI-Inc/lunaverse-ide
source_revision: 49adbc3fb31362195ed4b6e1a7111aaab3e89dff
initial_evidence_revision: 14c089322ba14c06b65fcb17d9ecdfd4d807b8d3
evidence_type: incremental-source-review-and-targeted-local-checks
---

# 收尾主线差异

2026-09-15 收尾 fetch 发现 main 从初始证据版本前进一条提交到 49adbc3fb31362195ed4b6e1a7111aaab3e89dff，提交时间 2026-09-15T12:55:38+08:00。完整差异只有以下六个文件：

- 删除 .codex/skills/lunaverse-git-delivery/SKILL.md。
- 删除 .codex/skills/lunaverse-worktree-create/SKILL.md。
- 删除 .codex/skills/lunaverse-worktree-preview/SKILL.md。
- 新增 docs/development/README.md。
- 新增 docs/development/git-and-pull-request.md。
- 新增 docs/development/worktree-build-test.md。

已通读完整差异。新的开发指南区分隔离工作区、依赖复用、聚焦测试、UI host、Beta 热补、完整打包与正式发布；本机专用 Skill 不再作为仓库共享开发流程。该变更不涉及 agents/**/skills 产品技能、运行时、配置、vendor、lockfile 或发布 workflow。原固定引用仍保留初始证据 SHA，变化部分引用新 SHA，不虚构所有测试最初就运行在新版本上。

隔离审查副本已快进到最终 SHA，原用户 checkout 的六个已修改文件及一个未跟踪草稿目录保持原状。本次没有改 IDE 源码或安装应用。

## 聚焦测试的追加与复跑

在 Node 22 下，初始版本和最终版本均实际执行并通过以下 Vitest：

| 文件 | 测试数 | 结果 |
|---|---|---|
| packages/shared/src/node/book-layout.test.ts | 17 | pass |
| packages/shared/src/node/creator-step-progress.test.ts | 30 | pass |
| packages/agent-runtime/src/runtime-selector.test.ts | 6 | pass |

合计 53 项通过。没有调用完整根构建、生产 API 或付费 provider。

最终 SHA 复跑初始记录所列八个 Node 测试文件：仍为 83 tests / 73 pass / 1 fail / 9 skip。唯一失败仍来自 capability inventory 漏登记两个操作；check-guidance 复跑仍报 HANDOFF.md:76 指向未跟踪 AGENTS.md 的一个断链。不能把这些失败或跳过计为通过。

LS 在线精确上游完整树验证及 33 个 Skill 完整包检查在初始 SHA 通过；最终增量没有修改相应源码与输入。这里保留证据所属版本，不称其生产上线已验收。

## Wiki 本地验收

- pytest：43 passed，1 条第三方依赖弃用警告。
- 实际 MCP 结构检查：零结构错误。提交前有一条 rename 历史页 stale 提示，依据 Git 提交时间计算，应在本次提交后再次复核；此原始记录不预先宣称已消失。
- 覆盖：原有 87 页均有处置理由；32 页字节级保留；23 页历史正文、24 页跨项目正文完整保留；11 页现行专题中含 5 个新增。
- 初始证据固定源码链接覆盖 120 个不同路径，全部存在。
- 原有 raw 无修改；新增文本的 GitHub token、长 sk token、JWT、私钥头、带密码数据库 URL 扫描均零命中。这只是有限模式检查，不是完整安全审计。
- 实际 MCP 查询 Pi、INNER_THOUGHT、lunaverse_produce、creator-step-progress 均返回相关结果。

上述为本地观察，不预写 push 或 GitHub CI 成功。最终发布结果应从同批 Git 提交和对应 CI 获取。线上登录、Skill 指针、付费生产、安装版 UI、完整打包、发布到玩家可见和独立后端仍未验证。
