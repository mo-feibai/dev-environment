# 什么可以进 AGENTS.md（判定规则）

适用范围：`C:\Users\Administrator\.config\opencode\AGENTS.md`（全局指令，每轮全量常驻加载）。
本文件**不注册进 `opencode.json` 的 `instructions`**（零常驻成本），由 AGENTS.md 末尾的指针行指向。

判定规则来自 2026-10-14 的一次实测拆分：61 行 / 3563 字符 / 8 节 → 26 行 / 1239 字符 / 4 节，常驻指令合计 3269 → 2139 est tokens（-35%），信息零丢失。

## 规则 1：双条件判据

一条内容必须**同时**满足两条才写进 AGENTS.md：

1. **每轮都生效**——与任务类型无关（任务域细则一律走 skill）；
2. **加载 skill 失败时没有兜底**——若某个 skill 承载了它而没被加载，行为会立刻失控。

只要有一条不满足，就写进 skill（或已有的 `skills-usage.md` / 其他 `instructions` 文件）。

实证：文枢 3 条、Bruno 6 条、接口测试 1 条共 1949 字符删除后零损失，全部只满足「不满足①」。

## 规则 2：规则本体留，证据/示例/枚举删

超过一行的附加内容，先判断它是**规则**还是**理由**。理由类一律删或移走：上游 issue 编号、踩坑案例、对比示例、枚举全表、「本条同时是另一节的落地方式」这类自述。

理由是当初挣来的，挣过之后不必每轮再付 token。真要留证据，放进对应 skill 的正文。

实证：本轮删掉 3 处上游 issue 编号、1 处 Chrome 登录示例、1 处 9 项 emoji 枚举、1 处跨节自述、1 处重复的「连续三次失败即停」。

## 规则 3：skill 或常驻文件已覆盖的细则，不留指针行

`skills-usage.md` 经 `instructions` 常驻加载，是唯一的 skill 路由源。因此：

- 不要在 AGENTS.md 里复述 skill 清单、映射表或触发条件；
- 不要为已在常驻文件里的 skill 单独写一行「→ 必须先加载 X」；
- 允许的唯一自述是「本文件不复述条目」。

实证：拆分后我一度同时写了 L13「本文件不复述条目」和 L15 的 wenshu/openapi-bruno 路由行，自相矛盾，本轮已删 L15。

## 规则 4：写 skill 相关指令前，先验证该字段 opencode 是否认

opencode 的 SKILL.md frontmatter 只认 `name` / `description` / `license` / `compatibility` / `metadata`。`disable-model-invocation`、`allowed-tools` 是 Claude Code 字段，**opencode 静默忽略——不报错、也不生效**。等价字段是 `metadata["opencode/autoinvoke"]="false"` 与 `metadata["opencode/slash"]="true"`。

照抄别处写法会写出「看着在管、实际没管」的规则。硬限制：`description` 必须 1–1024 字符。

实证：`skills/i-have-adhd/SKILL.md` L4 的 `disable-model-invocation: true` 对 opencode 无效，因此 AGENTS.md 里的输出风格段是该风格唯一的常驻执行点，不可删。

## 规则 5：改动后必须实测并如实报告

每次改 AGENTS.md 后跑一次字符数与 est tokens 统计（中文按 1.0 token/字、西文按 0.28 token/字符估算），把**实测值**与预估值的差异写进回复。

实证：第一次拆分我预估省约 2000 tokens，实测只省 890（被删的 4 节里有 3 节本来就很短，而压缩后的指针行反而比原文长）。感觉变小不算验收。

## 反例：以下内容不该出现在 AGENTS.md

| 内容 | 该去哪 |
| --- | --- |
| Bruno/OpenAPI 文件格式、workspace.yml 注册 | `skills/openapi-bruno` |
| 文枢工具选择、状态翻转确认 | `skills/wenshu` |
| 输出风格 10 条完整规则与坏例子 | `skills/i-have-adhd` |
| 接口测试落点 `E:\project-docs\<project>\api-tests\` | `skills/openapi-bruno` + `skills-usage.md` 第 10 条 |
| skill 高频路由表 | `skills-usage.md`（常驻加载） |
| 把某节拆到另一个 `instructions` 文件 | 无效——所有 `instructions` 都是全量常载，只有 skill 懒加载，省不到 token |

## 维护

- 新增 skill 时，同步更新 `skills-usage.md` 的 11 条对照表（该文件 L56 已写此约定），**不要**因此往 AGENTS.md 加行。
- 改动前先备份：`Copy-Item AGENTS.md AGENTS.md.bak-<yyyyMMdd>-<原因>`（已有 `AGENTS.md.bak-20261014-presplit` 与 `AGENTS.md.bak-20261014-pretrim`）。
- 改动后核对指针行仍指向本文件。
