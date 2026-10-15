# configs

`~/.config/opencode` 下**指令栈**三份文件的副本，正文与线上一致，随本仓库版本化（用于换机还原与历史追溯）。

| 文件 | 线上位置 | 说明 |
| --- | --- | --- |
| `opencode/AGENTS.md` | `~/.config/opencode/AGENTS.md` | 全局行为规则（输出风格、Skill 加载纪律、交互、Git 提交） |
| `opencode/agents-md-rules.md` | `~/.config/opencode/agents-md-rules.md` | 判定「什么该写进 AGENTS.md」的规则 |
| `opencode/skills-usage.md` | `~/.config/opencode/skills-usage.md` | Skill 路由表（经 `instructions` 常驻加载） |

改动线上文件后同步过来：

```powershell
Copy-Item ~\.config\opencode\AGENTS.md,~\.config\opencode\agents-md-rules.md,~\.config\opencode\skills-usage.md <仓库>\configs\opencode\
```

**不含密钥与凭据的配置**（如 `opencode.json`、`wenshu.json`、`service.json`）不纳入本目录；它们的结构与关键项说明见 [docs/06-configs-and-agents.md](../docs/06-configs-and-agents.md)。
