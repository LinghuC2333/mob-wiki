---
title: Lunaverse IDE 校准最终主线 — 开发指南撤回
created: 2026-09-15
source_revision: 442fd53692d077c1e601231d79792c6c50b430e3
previous_observed_revision: 49adbc3fb31362195ed4b6e1a7111aaab3e89dff
initial_evidence_revision: 14c089322ba14c06b65fcb17d9ecdfd4d807b8d3
---

最终同步再次观察到一条 main 提交：442fd53692d077c1e601231d79792c6c50b430e3，2026-09-15T13:15:44+08:00，revert: 移除 PR #5 的开发规范文档 (#19)。完整差异只删除上一观察版本新增的 docs/development/README.md、git-and-pull-request.md、worktree-build-test.md；已通读全部删除内容。

因此上一补充记录中的“新增共享开发指南”仅是 49adbc3fb 时刻的事实，已经撤回，不能作为最终版本通用开发规范。Wiki 当前规则页与运维页应移除该指南的执行建议，只保留撤回记录和历史精确链接。三份本机 .codex/skills 也没有恢复。

从最初证据 14c089322 到最终 442fd5369 的净差异，只有删除三份本机开发 Skill。运行时、agents/**/skills 产品技能、配置、vendor、测试、依赖和发布 workflow 均未改变。隔离审查副本已切到最终精确 SHA，原用户 checkout 未修改。

实际测试所属版本保持原记录：53 个 Vitest 在 14c089322 与 49adbc3fb 都通过；83 个 Node 测试在这两个版本均为 73 pass、1 fail、9 skip；guidance 的 AGENTS 断链两次均存在。442fd5369 的应用实现和测试输入相同，但不将“差异核对”伪报为在该 SHA 又运行了一遍测试。线上验收边界不变。

校准是一份带明确检查时间与 exact SHA 的快照，不是实时订阅。将来 main 再前进，应另做增量入库；已有 raw 保留原时刻事实，不覆盖。
