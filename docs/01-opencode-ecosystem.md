# OpenCode / OpenChamber 生态

## 应用

| 软件 | 版本 | 简介 | 链接 |
| --- | --- | --- | --- |
| OpenChamber | 1.23.2 | 基于 OpenCode 的桌面 Agentic 开发环境（项目管理、会话、定时任务、GitHub 集成） | https://github.com/openchamber/openchamber |
| OpenCode | 桌面集成 1.18.4 / CLI 1.18.30 | 开源 AI 编码 agent，支持多模型提供方与插件 | https://opencode.ai ｜ https://github.com/sst/opencode |
| Magic Context Dashboard | 0.16.0 | cortexkit 的上下文/记忆仪表盘 | https://github.com/cortexkit/magic-context |

## 插件

| 插件 | 作用 | 来源 |
| --- | --- | --- |
| `oh-my-opencode-slim@latest` | 精简的 agent 工作流套件（含配套 skills） | npm |
| `@cortexkit/magic-context` / `@cortexkit/opencode-magic-context` | 跨会话记忆与上下文压缩 | https://github.com/cortexkit/magic-context |
| `opencode-pty` | 让 agent 在 PTY 中运行常驻/交互进程 | https://github.com/shekohex/opencode-pty |
| `commandcode-go-opencode-provider` | Command Code 模型提供方（本地 `file://` 插件） | 本地私有 |
| `plugins/wenshu.ts` | 文枢文档库桥接（本地插件） | 本地私有 |

> 全局配置同时存在 `opencode.json` 与 `opencode.jsonc`（后者含 provider 定义），插件条目在两处都登记，避免加载歧义。

## MCP

| 名称 | 状态 | 说明 |
| --- | --- | --- |
| `@benborla29/mcp-server-mysql` 2.0.9 | 已安装（全局 npm） | MySQL MCP server：https://www.npmjs.com/package/@benborla29/mcp-server-mysql |
| `picident` | 已配置、**禁用** | 本地 MCP server；其模型提供方密钥由环境变量注入，不入库 |

## Skills（共 23 个）

### 项目自带（`~/.config/opencode/skills`，11 个）

| Skill | 用途 |
| --- | --- |
| `clonedeps` | 把关键依赖源码克隆到忽略目录，便于阅读库内部实现 |
| `codemap` | 为陌生仓库生成分层代码地图 |
| `deepwork` | 高风险、多阶段工作的编排流程（含评审闸门） |
| `i-have-adhd` | 输出风格：先给下一步、编号、限时、可见进度 |
| `oh-my-opencode-slim` | 本插件生态的配置与调优指南 |
| `reflect` | 复盘近期工作，沉淀可复用 skill/配置 |
| `release-smoke-test` | 发布前对插件产物与运行时做冒烟测试 |
| `simplify` | 不改变行为的前提下简化代码 |
| `verification-planning` | 为非平凡改动规划可验证的证据路径 |
| `wenshu` | 文枢文档库维护（SQL 迁移、接口文档、ADR，走 `wenshu_*` 工具） |
| `worktrees` | 用 Git worktree 做隔离开发 |

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

## 关键路径

| 用途 | 路径 |
| --- | --- |
| 全局配置 | `~/.config/opencode/opencode.json`、`~/.config/opencode/opencode.jsonc` |
| 本地插件 | `~/.config/opencode/plugins/` |
| 全局 Skills | `~/.config/opencode/skills/`、`~/.agents/skills/` |
| OpenChamber 配置与登录信息 | `~/.config/openchamber/`（含凭据，禁止入库） |
