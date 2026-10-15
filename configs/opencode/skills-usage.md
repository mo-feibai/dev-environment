# Skill 加载要求（全局）

任务匹配 available_skills 中任一 skill 的 description 时，默认必须先调用 `skill` 工具加载，再动手；不得静默跳过。

## 高频强制对照（11 条）

1. 新增/修改/评审 Java 代码（含注释、Javadoc）→ `java-comments`
2. 非平凡功能实现或缺陷修复（动手前）→ `verification-planning`
3. 评审分支/PR/改动（"review since X"）→ `code-review`
4. 重构、可读性/复杂度优化 → `simplify`
5. 报错、异常、性能回退排查 → `diagnosing-bugs`
6. 文枢文档库新建/更新/状态翻转 → `wenshu`
7. 模块接口/接缝设计、深化与可测性评估 → `codebase-design`
8. OpenCode 自身配置（agents/skills/plugins/commands）→ `customize-opencode`
9. API 文档、OpenAPI 生成、Bruno 同步或 Bruno 看不到接口 → `openapi-bruno`
10. 开发完成后的接口测试、Playwright 测试（E2E/组件/flaky/CI）→ `playwright-best-practices`；接口测试文件/测试工程落点 `E:\project-docs\<project>\api-tests\`，不落进业务项目仓库目录
11. 浏览器交互、一次性页面探查、多角色并行验证、录屏/HAR 复现、a11y 预扫、Electron/Slack 自动化 → `agent-browser`

## 浏览器与测试：三层分工（不要混用）

| 工具 | 层级 | 何时用 | 产物落点 |
| --- | --- | --- | --- |
| `openchamber_web`（harness 内置） | 用户可见的交互面板 | 复用用户真实登录态、用户想亲眼看着点、单页快速操作 | 面板截图 |
| `agent-browser`（全局 CLI，版本随 npm latest 升级） | 无人值守执行引擎 | 后台跑、多 `--session` 并行、`auth vault`、`batch` 单调用多步、HAR/录屏、`a11y --json` 预扫、Electron/Slack | `E:\project-docs\<project>\` 下临时目录 |
| `playwright-best-practices` | 测试工程规范 | 产出可重复、可回归、进 CI 的测试代码 | `E:\project-docs\<project>\api-tests\` |

路由规则：

1. 要产出**可回归测试代码** → 只能用 `playwright-best-practices`。
2. 要**当场一次性操作**且用户想看着 → 用 `openchamber_web`。
3. 要**无人值守 / 多会话并行 / 录屏 / HAR / 凭据 vault / 先 a11y 探一遍再决定写哪些断言** → 用 `agent-browser`，动手前必须先跑 `agent-browser skills get core` 取版本匹配手册，**不得凭记忆拼命令**。
4. 三者串联：agent-browser 探页面摸清选择器 → playwright-best-practices 固化成断言 → openchamber_web 复看确认。
5. `agent-browser` 的 `@eN` 引用在页面变化后立即失效，点击/提交/重渲染后必须重新 `snapshot -i`。
6. 禁止裸 `wait 2000`（两边同规）：改用 `wait --text` / `--url` / `--fn`，Playwright 侧对应 `core/assertions-waiting.md`。
7. 涉及凭据一律走 `auth save` / `cookies set --curl <file>`，禁止把密码写进命令行参数。

## 兜底条款

其余 skill（含 domain-modeling、tdd、implement 等）以各自 description 为准，任务匹配即加载；不确定是否匹配时，先加载再判断。

## 例外与透明度

- 用户明确要求快速处理或跳过流程时，可跳过加载。
- 不改语义的单行修正（注释错别字、提示文案、常量取值）豁免强制加载。
- 任一情况跳过时，需一句话说明「为何跳过」——这是本要求唯一的硬约束（可观测性）。

## 加载纪律

- 重流程 skill（`deepwork`、`codemap`、`release-smoke-test`、`worktrees`）仅在任务明确属于其场景时加载。
- 同一会话已加载过的 skill 不重复加载；跨任务域需重新判断。
- 加载失败或明显不适用时，在回复中说明原因，不得静默跳过。
- 实现类任务交付前，按对应 skill 内的检查清单自查（如 java-comments 的类型/方法/字段/行内注释检查表）。

## 维护

skill 清单变化时同步更新本对照表。
