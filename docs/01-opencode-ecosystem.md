# OpenCode / OpenChamber 生态

## 应用

| 软件 | 版本 | 简介 | 链接 |
| --- | --- | --- | --- |
| OpenChamber | 2.3.3 | 基于 OpenCode 的桌面 Agentic 开发环境（项目管理、会话、定时任务、Git 集成） | https://github.com/openchamber/openchamber |
| OpenCode | 桌面 1.18.4 ／ 全局 CLI 1.18.35 ／ OpenChamber 托管 CLI 2.0.29 | 开源 AI 编码 agent，支持多模型提供方与插件 | https://opencode.ai ｜ https://github.com/sst/opencode |
| Magic Context Dashboard | 0.20.0 | cortexkit 的上下文/记忆仪表盘 | https://github.com/cortexkit/magic-context |

> OpenChamber 当前运行的是**本地补丁构建** `%LOCALAPPDATA%\Programs\openchamber-patched\app`（与官方安装目录 `@openchamberelectron` 同为 2.3.3，差异只在补丁）。补丁脚本在 `patch/`：`apply-patch.ps1`、`patch-asar.mjs`、`apply-webdist.mjs`、`check-version.mjs`、`upgrade-and-patch.ps1`。该构建自带托管 CLI `resources\opencode-cli\opencode.exe`（OpenCode **v2** 2.0.29），与 scoop 安装的全局 CLI 1.18.35（**v1**）并存——注意区分，两个大版本配置文件不互通。

## 模型提供方

| Provider | 配置位置 | 状态 |
| --- | --- | --- |
| `agentrouter` | `opencode.json` → `providers`（OpenAI 兼容，登记 6 个模型） | 启用 |
| `commandcode` | `opencode.json` → `provider`（本地 `file://` 插件，Command Code 模型目录） | 已登记、在 `disabled_providers` 中禁用 |

## 插件

| 插件 | 作用 | 来源 |
| --- | --- | --- |
| `@cortexkit/opencode-magic-context@0.49.0` | 跨会话记忆与上下文压缩 | https://github.com/cortexkit/magic-context |
| `oh-my-opencode-slim@3.0.4` | 精简的 agent 工作流套件（subagent 角色、skills、companion、preset） | npm |
| `pty-v2` | 本地薄壳插件：在 v2 宿主下注册 `pty_*` 工具 | 本地私有 |
| `plugins/wenshu.ts` | 文枢文档库桥接（`wenshu_*` 工具） | 本地私有 |
| `plugins/agentrouter-tool-schema.ts` | 为 AgentRouter 通道补齐工具 schema 的 `required` 字段 | 本地私有 |
| `commandcode-go-opencode-provider` | Command Code 模型提供方（本地 `file://` 插件） | 本地私有 |

> 前三个来自 `opencode.json` 的 `plugins` 数组；旧版曾同时在 `opencode.jsonc` 登记，该文件已移除并归档到 `~/.config/opencode/_archived-v1-20260924/`。OpenChamber 侧另会注入 `agent-tool`、`mcp-reconnect` 两个桥接插件。逐文件说明见 [06 全局配置与 Agents](06-configs-and-agents.md)。

## MCP

| 名称 | 状态 | 说明 |
| --- | --- | --- |
| `dbx` | 已配置、启用 | DBX 自带 MCP（`~/.dbx/bin/dbx-mcp.exe`）：数据库连接、库表结构与查询 |
| `picident` | 已配置、**禁用** | 本地 MCP server；密钥在配置的 `environment` 中注入（不入库） |
| `@benborla29/mcp-server-mysql` 2.0.9 | 已安装（全局 npm） | MySQL MCP server，启动脚本 `~/.local/share/opencode/mcp_mysql_launcher.ps1` |
| `context7`、`gh_grep` | 运行时可用 | 会话内可调用，但本地 `opencode.json` 里没有对应配置——来源待确认 |

## Skills（三层来源，共 27 个）

### 用户级（`~/.config/opencode/skills`，6 个）

| Skill | 用途 |
| --- | --- |
| `agent-browser` | 浏览器自动化 CLI：页面交互、抓取、截图、无人值守多会话、HAR/录屏、Electron 自动化 |
| `frontend-design` | 新 UI 或改版时的视觉方向：排版、字体，避免模板脸 |
| `i-have-adhd` | 输出风格：先给下一步、编号、限时、可见进度 |
| `openapi-bruno` | OpenAPI 文档与 Bruno 同步工作流 |
| `playwright-best-practices` | Playwright 测试工程规范（E2E、组件、flaky、CI、鉴权等） |
| `wenshu` | 文枢文档库维护（SQL 迁移、接口文档、ADR，走 `wenshu_*` 工具） |

### 社区 / 自研（`~/.agents/skills`，12 个）

| Skill | 来源 | 用途 |
| --- | --- | --- |
| `find-skills` | vercel-labs | 在 skills.sh 注册表发现并安装 skill |
| `grilling` | mattpocock/skills | 访谈原语：按 frontier 一轮轮追问，直到共识 |
| `grill-me` | mattpocock/skills | 调起 grilling 的入口（用户触发） |
| `grill-with-docs` | mattpocock/skills | 访谈同时产出 CONTEXT.md 与 ADR |
| `domain-modeling` | mattpocock/skills | 维护领域词汇表与 ADR |
| `codebase-design` | mattpocock/skills | 深模块设计词汇：接口、接缝、深度、杠杆 |
| `diagnosing-bugs` | mattpocock/skills | 硬 bug 诊断循环：先造红色反馈回路 |
| `implement` | mattpocock/skills | 按 spec/工单施工，内部驱动 tdd + code-review |
| `code-review` | mattpocock/skills | 双轴审查（规范轴 + 需求轴，并行子代理） |
| `tdd` | mattpocock/skills | 红-绿-重构循环参考 |
| `setup-matt-pocock-skills` | mattpocock/skills | 每个仓库初始化一次（issue tracker、标签、文档布局） |
| `java-comments` | 自研 | Java 注释规范（类/方法/字段/行内 + 正反例） |

### 插件自带（随 `oh-my-opencode-slim` 版本，9 个）

| Skill | 用途 |
| --- | --- |
| `clonedeps` | 把关键依赖源码克隆到忽略目录，便于阅读库内部实现 |
| `codemap` | 为陌生仓库生成分层代码地图 |
| `deepwork` | 高风险、多阶段工作的编排流程（含评审闸门） |
| `loop-engineering` | 循环工程运行时（grill + monitor） |
| `oh-my-opencode-slim` | 本插件生态的配置与调优指南 |
| `reflect` | 复盘近期工作，沉淀可复用 skill/配置 |
| `simplify` | 不改变行为的前提下简化代码 |
| `verification-planning` | 为非平凡改动规划可验证的证据路径 |
| `worktrees` | 用 Git worktree 做隔离开发 |

## 关键路径

| 用途 | 路径 |
| --- | --- |
| 全局配置 | `~/.config/opencode/opencode.json`（旧 `opencode.jsonc` 已归档） |
| 指令栈 | `~/.config/opencode/AGENTS.md`、`agents-md-rules.md`、`skills-usage.md` |
| 本地插件 | `~/.config/opencode/plugins/`、`~/.config/opencode/pty-v2/` |
| 全局 Skills | `~/.config/opencode/skills/`、`~/.agents/skills/` |
| 自定义 subagent | `~/.config/opencode/agents/explore.md` |
| 插件缓存 | `~/.cache/opencode/packages/`（`@cortexkit/`、`oh-my-opencode-slim@*`） |
| 运行时数据 | `~/.local/share/opencode/`（`auth.json`、`opencode.db`、`storage/`、`tool-output/`） |
| OpenChamber 配置与登录信息 | `~/.config/openchamber/`（含凭据，禁止入库） |

> 配置逐文件说明、Agents 清单与换机重建顺序见 [06 全局配置与 Agents](06-configs-and-agents.md)；踩坑记录见 [07 经验手册](07-playbook.md)。
