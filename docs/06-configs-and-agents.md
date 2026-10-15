# 全局配置与 Agents

OpenCode 的全局配置分三层：**配置文件**（`~/.config/opencode/`）、**指令栈**（写行为的 Markdown）、**运行时数据**（`~/.local/share/opencode/`）。本文只描述结构与关键项，值以文件为准。

## 配置根目录地图

```
~/.config/opencode/
├─ opencode.json              全局配置（provider / plugins / mcp / instructions / …）
├─ AGENTS.md                  全局行为规则
├─ agents-md-rules.md         AGENTS.md 的维护规则
├─ skills-usage.md            Skills 路由表（经 instructions 常驻加载）
├─ oh-my-opencode-slim.jsonc  插件 preset
├─ cli.json / tui.jsonc / tui-preferences.jsonc   CLI 与 TUI 偏好
├─ agents/explore.md          自定义 subagent
├─ plugins/                   本地插件（wenshu.ts、agentrouter-tool-schema.ts）
├─ pty-v2/                    PTY 工具的 v2 薄壳插件
├─ skills/                    用户级 skills（6 个）
├─ node_modules/              @opencode-ai/plugin、opencode-pty（被 .gitignore 忽略）
└─ _archived-v1-20260924/     v1 时代配置归档（旧 opencode.jsonc 等）
```

## opencode.json 结构

| 键 | 内容 |
| --- | --- |
| `providers` | 自定义 provider（`agentrouter`，OpenAI 兼容，登记 6 个模型） |
| `provider` | 传统 provider 定义（`commandcode`，本地 `file://` 插件 + 模型目录） |
| `disabled_providers` | `["commandcode"]` |
| `plugins` | `@cortexkit/opencode-magic-context@0.49.0`、`oh-my-opencode-slim@3.0.4`、本地 `pty-v2` 目录 |
| `mcp` | `dbx`（启用）、`picident`（禁用，`environment` 里含密钥） |
| `instructions` | `["skills-usage.md"]` |
| `compaction` | `{ "auto": false }` |
| `warming` | `true` |
| `websearch` | `{ "provider": "random" }` |

## 指令栈

| 文件 | 作用 |
| --- | --- |
| `AGENTS.md` | 全局行为规则：输出风格（先给下一步、编号、限时；引用 `i-have-adhd`）、Skill 加载纪律、用户交互（统一走 `question` 工具、提问前自查、一次最多 2 问）、Git 提交（不主动提交；提交消息 `<emoji> <type>(<scope>): <中文描述>`） |
| `agents-md-rules.md` | 判定「什么该写进 AGENTS.md」的规则；改 `AGENTS.md` 前先读 |
| `skills-usage.md` | Skill 路由表：11 条高频场景 → 对应 skill；浏览器/测试的三层分工（面板 / agent-browser / playwright）；兜底与例外条款 |

## 其他配置文件

| 文件 | 关键内容 | 换机处理 |
| --- | --- | --- |
| `oh-my-opencode-slim.jsonc` | preset `default`：orchestrator / oracle / explorer / fixer 的 MCP 白名单 `["*"]` | 直接带走 |
| `cli.json` | 插件登记（magic-context **0.42.6**）+ 主题 `nord` + 动画 | 带走；版本钉法与 `tui.jsonc` 不一致，见 [07](07-playbook.md) 现状坑 |
| `tui.jsonc` | TUI 插件登记（magic-context **@latest**） | 带走 |
| `tui-preferences.jsonc` | TUI 插件偏好（`magic-context.collapsed`） | 带走 |
| `agents/explore.md` | 只声明 `mode: subagent`，无自定义 prompt | 带走 |
| `plugins/wenshu.ts` | 文枢工具桥接，读 `wenshu.json` | 带走 |
| `plugins/agentrouter-tool-schema.ts` | 为缺 `required` 的工具 schema 补 `required: []` | 带走 |
| `pty-v2/` | PTY 工具 v2 薄壳（依赖 `node_modules/opencode-pty`） | 带走目录，依赖重装 |
| `node_modules/`、`package.json`、`package-lock.json` | `@opencode-ai/plugin` 1.17.3、`opencode-pty` 0.4.0 | 重新 `npm i` |
| `wenshu.json` | **含 apiToken** | **不入库**，换机重新生成 |
| `service.json` | **含口令** | **不入库** |
| `_archived-v1-20260924/` | v1 时代归档（旧 `opencode.jsonc`、备份） | 可选 |
| `*.py`、`shot-*.png` | 调试脚本与截图（scratch，30+ 个文件） | 不带走，建议清理 |

## Agents

宿主内置 + 插件提供的 subagent 共 7 个：

| Agent | 来源 | 用途 |
| --- | --- | --- |
| `explore` | 用户自定义（`agents/explore.md`） | 代码库快速探索 |
| `explorer` | `oh-my-opencode-slim` | 快速检索与模式匹配 |
| `designer` | `oh-my-opencode-slim` | UI/UX 设计、审查与实现 |
| `fixer` | `oh-my-opencode-slim` | 快速实现 |
| `librarian` | `oh-my-opencode-slim` | 外部文档与库研究 |
| `oracle` | `oh-my-opencode-slim` | 战略技术顾问 |
| `general` | 宿主内置 | 通用多步任务 |

> 插件另带 orchestrator / council / councillor / observer 等**角色定义**，由 preset 体系加载，不作为独立 subagent 直接调用。插件版本升级时角色清单可能变化，以会话内实际可见的 subagent 列表为准。

## 其他 AI 编码工具（待确认）

| 工具 | 证据 | 状态 |
| --- | --- | --- |
| Codex CLI | `~/.codex/`（config.toml、sessions、goals/memories sqlite、`.sandbox-bin/codex.exe` 0.119.0-alpha.28） | 不在 PATH，配置指向内网模型网关；是否继续使用待确认 |
| Cursor | `~/.cursor/`（extensions、mcp.json、argv.json），未见安装目录 | 疑似仅剩配置残留 |

## 换机重建顺序

1. 装运行时：`scoop install opencode`（v1 CLI）与 OpenChamber 安装包（含 v2 托管 CLI）。
2. 恢复 `~/.config/opencode`：`opencode.json` + 指令栈三个 md + `agents/` + `plugins/` + `pty-v2/` + `skills/`；在目录内 `npm i` 重装 `node_modules`。
3. 恢复 `~/.agents/skills`（12 个）与 `~/.cache/opencode/packages`（插件缓存，可让宿主自动重下）。
4. 重新登录/生成凭据：OpenChamber 登录、provider 密钥（agentrouter 等）、`wenshu.json` 的 apiToken、`picident` 令牌；**不要**从旧机拷贝 `opencode.db` / `auth.json`。
5. 校验：`opencode --version`；会话内 `wenshu_*`、`pty_*`、`dbx` 工具可见；skills 数量 27（6 + 12 + 9）。

## 运行时数据（`~/.local/share/opencode/`）

| 路径 | 说明 |
| --- | --- |
| `opencode.db` | v2 引擎的凭证与会话库（`credential` 表等），**含登录凭据** |
| `auth.json` | v1/CLI 旧凭证库（`opencode auth` 读写） |
| `mcp_mysql_launcher.ps1` | MySQL MCP 的启动脚本 |
| `storage/`、`tool-output/`、`snapshot/`、`repos/`、`worktree/` | 会话、工具输出、快照等运行时数据 |

> 凭证库的关系与清理注意事项见 [07 经验手册](07-playbook.md)。
