---
title: Lunaverse IDE — Pi 运行时、统一登录与 AI 交互
tags:
- lunaverse-ide
- ai-integration
- cline
- tab-completion
- architecture
sources:
- raw/2026-09-15-lunaverse-ide-main-calibration.md
- raw/2026-09-15-lunaverse-ide-calibration-final-main.md
- raw/2026-09-15-lunaverse-ide-calibration-verification.md
created: '2026-05-18'
updated: '2026-09-15'
last_reviewed: '2026-09-15'
status: current-source-snapshot
source_revision: 442fd53692d077c1e601231d79792c6c50b430e3
evidence_revision: 14c089322ba14c06b65fcb17d9ecdfd4d807b8d3
---

当前产品的主 Agent 使用 Pi；Tab 补全是独立的单轮模型调用。本文以固定主线代码说明宿主、模型协议与权限边界，旧 Cline + Codex shim 的设计只保留为历史参考。

本页校准截至 main `442fd53692d077c1e601231d79792c6c50b430e3`；实现引用保留最初核对的 `14c089322ba14c06b65fcb17d9ecdfd4d807b8d3`。期间两次开发资料变更已核对，最终净差异仅删除三份本机开发 Skill，产品实现未变。版本与测试归属见 [[syntheses/lunaverse-ide-calibration-2026-09]]；这不是实时订阅。

## 两类交互，不是两个主 Agent

| 交互 | 所属代码 | 当前行为 |
|---|---|---|
| Lunaverse Agent | `ls-agent` + `agent-runtime` | 独立作品会话、工具调用、历史、文件恢复、宿主生产及发布能力 |
| Tab 幽灵文本 | `ls-workbench/inline-completion.ts` + `agent-adapter` | `.ls` 上的 InlineCompletionItemProvider，默认启用，350ms 防抖，显式触发跳过防抖；取消会中止旧请求 |

Tab 不是纯本地词法补全。它构造上下文，创建 AgentAdapter session，经保留的单轮 ChatBackend 请求模型；配置或网络失败时不显示补全。不要将旧 Wiki 的“Tab 不调 LLM”或“固定 DeepSeek”当成现状。

主会话可以使用宿主支持的独立审查、子任务和生产流程；“单一可见 Agent”并不保证进程中只有一次模型调用。领域目录不是四个用户必选的常驻专家。

## Pi 选择与路由

`runtime-selector.ts` 只返回 runtimeId `pi`，reason 可为：`existing-session`、`incompatible-existing-session`、`pi-only`、`unsupported-wire-protocol`、`pi-disabled`、`pi-unavailable`。选择结果不等于可用：缺少登录、协议或可用模型时宿主拒绝启动，不回退到 Codex、Cline 或另一个收费链路。

`runtime-config.ts` 解析登录、规范化网关地址、刷新模型目录，并分开保存产品模型 ID 与 wire 模型 ID。目录描述原生协议、endpoint、图像能力、推理强度、上下文/输出限制及目录 revision；支持的协议是 `anthropic-messages`、`openai-responses`、`chat-completions`。新建/恢复运行会要求刷新目录，显示缓存不应被当成付费会话准入凭证。

`agent-runtime/package.json` 固定 Pi core/ai/coding-agent `0.84.2`；主仓还有固定的补丁与子任务/目标依赖。版本是本次代码快照，不能外推线上目录里可选的模型。

## 统一登录与秘密边界

普通成员用 Lunaverse: Login。默认 `lunaverse.gatewayBaseUrl` 是 `https://ide-api.playlunaverse.com`；登录由宿主共享给 Agent、生产和发布。运行时不要求 `codex login`，更不应从其他项目 `.env` 拷供应商密钥。

`safeAgentChildEnvironment` 只投影允许的环境字段，包括当前书路径、受管理 CLI 路径、网关与本次登录上下文；不继承任意 shell/扩展宿主秘密。生产供应商、存储、观测密钥不是写入 Wiki 或书籍工程的材料。旧 `lunaverse.authToken`、Agent Adapter 开发覆盖和部分旧 `claudeSdk` 设置存在兼容读取，不代表推荐配置。

## Agent 设置

| 设置 | 默认 | 含义 |
|---|---|---|
| `lunaverse.agent.pi.enabled` | `true` | 关闭后 Agent 不可用，无旧运行时回退 |
| `lunaverse.agent.autoContinueWorkflows` | `true` | 生产工作流返回时自动续跑所属会话；资源范围配置 |
| `lunaverse.agent.pi.baseUrl` | 空 | 原生协议 endpoint 开发覆盖；空值复用登录网关和模型目录 |
| `lunaverse.agent.pi.model` | `gpt-5.6-sol` | 配置首选模型；实际可用项仍由当前目录决定 |
| `lunaverse.agent.pi.effort` | `high` | `low/medium/high/xhigh/max`；轮次边界生效，受模型支持约束 |
| `lunaverse.agent.pi.permissionProfile` | `auto` | `autonomous/auto/review/plan`，分别表达宿主管理自主、安全自动、敏感操作确认、仅规划 |
| `lunaverse.agent.pi.evalControllerPort` | `9878` | 1024–65535；只对有认证 evals.env fixture 的工作区使用 |
| `lunaverse.inlineCompletion.enabled` | `true` | 关闭 Tab Provider 后它不发送 AI 请求 |

生产路由另有 `minimal/auto/strict` 模式，默认 `auto`，与上表权限模式不是同一枚开关。无论模式如何，安全、文件完整性、付费准入、重复提交和发布约束均不得被创作上的软判断绕过。

## 状态、并发与恢复

宿主维护 Pi conversation、事件和文件检查点；恢复检查点要绑定原书的 scopeRoot。不要猜测会话存储路径或用旧 `CODEX_HOME` 清理说明处理当前会话。

外部 `lunaverse` CLI 和前台/子任务使用共享执行协调；本次源码的本地高水位为 64，单个批请求最多 256 项。它们不是云端供应商额度，也不是“每本书 64 个收费任务”的承诺。工具能力以当前实例 catalog 和 schema 为准。

网络中断、取消请求、Job 仍在运行和结果未知必须区分。应按原 request ID / job ID 读取真实结果，不能另起一个相同付费请求来猜成功与否。

## 代码命名与历史

部分投影、可观测性和兼容模块仍使用 `cline-*` 命名；这不证明产品仍以 Cline 运行。外部 Codex/Claude/Pi 的 IDE Control 接入也不是内置主运行时。

旧测试数字、Codex 0.130、`vendor-codex`、`stageCodexHome`、localhost Responses shim 和 Langfuse skill overlay 见 [[concepts/codex-runtime-and-verification-layers]]，不能作为当前验收清单。

当前生产操作见 [[concepts/lunaverse-ide-skills-and-production]]；测试证据和源仓未通过项见 [[syntheses/lunaverse-ide-calibration-2026-09]]。

## 核对来源

- [packages/agent-runtime/src/runtime-selector.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/agent-runtime/src/runtime-selector.ts)
- [packages/ls-agent/src/runtime-config.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-agent/src/runtime-config.ts)
- [packages/ls-agent/package.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-agent/package.json)
- [packages/agent-runtime/package.json](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/agent-runtime/package.json)
- [packages/ls-workbench/src/inline-completion.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-workbench/src/inline-completion.ts)
- [packages/ls-agent/src/dag-mode.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/ls-agent/src/dag-mode.ts)
- [packages/agent-runtime/src/pi-checkpoints.ts](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/packages/agent-runtime/src/pi-checkpoints.ts)
- [docs/runbooks/ide-cli.md](https://github.com/MobAI-Inc/lunaverse-ide/blob/14c089322ba14c06b65fcb17d9ecdfd4d807b8d3/docs/runbooks/ide-cli.md)
- [本次原始核对记录](../../raw/2026-09-15-lunaverse-ide-main-calibration.md)
- [补充验证与最终主线差异](../../raw/2026-09-15-lunaverse-ide-calibration-verification.md)
- [最终主线与开发指南撤回](../../raw/2026-09-15-lunaverse-ide-calibration-final-main.md)
