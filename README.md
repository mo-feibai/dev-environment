# dev-environment

本机（Windows 开发机）的软件环境清单与配置说明，用于换机、重装或交接时快速还原。

## 环境概览

| 项 | 值 |
| --- | --- |
| 主机 | Windows 10 专业版 · i5-13400F / 16 GB / GTX 1650 · C: 200 GB、D: 366 GB、E: 365 GB |
| 主机用途 | Java 后端（Spring Boot）+ MySQL / 达梦 + WSL 常驻服务（9 个自托管服务） |
| 工作盘 | `D:\`（工具与中间件）、`E:\`（JDK / Maven / work）、WSL Debian 13 |
| 包管理 | scoop、winget、nvm-windows、npm / pnpm（npmmirror 镜像） |
| 编辑器/IDE | IntelliJ IDEA、WebStorm、VS Code、Lithe |
| AI 编码环境 | OpenChamber 2.3.3 + OpenCode（双 CLI、三层 Skills、插件与 MCP） |

## 目录

- [01 OpenCode / OpenChamber 生态](docs/01-opencode-ecosystem.md) —— 应用、模型提供方、插件、MCP、27 个 Skills
- [02 数据库与中间件](docs/02-databases-and-middleware.md) —— MySQL、达梦、RabbitMQ、Elasticsearch、客户端工具
- [03 开发工具链](docs/03-dev-toolchain.md) —— 运行时、Scoop 包、JDK、Maven、IDE
- [04 客户端与桌面工具](docs/04-tools-and-apps.md) —— 终端、远程与内网穿透、网络代理、浏览器、桌面效率
- [05 WSL 自托管服务](docs/05-wsl-selfhosted.md) —— Redis、Nacos、RustFS、Caddy、Homepage、Mibao 保险箱、打包工坊、服务看板、文枢
- [06 全局配置与 Agents](docs/06-configs-and-agents.md) —— OpenCode 配置逐文件说明、subagent 清单、换机重建顺序
- [07 经验手册](docs/07-playbook.md) —— 已验证的坑与做法（模型、插件契约、网络证书、WSL）+ 现状问题修复建议
- [08 环境与换机还原](docs/08-environment-and-migration.md) —— 计划任务与自启、环境变量与 PATH、目录与备份、镜像源、主机信息、证书

## 说明

1. 版本与运行状态采集于 **2026-10-15**。
2. 本仓库**不包含**任何密钥、token、内网 IP、账号与凭据信息；配置类文件只登记「含密钥、不入库」。
3. 企业合规类（安全认证、终端管控）、授权类工具与系统个性化未列入本清单。
4. 表内链接优先使用官网，其次 GitHub 仓库；未确认项标注「待确认」。
5. 换机照 [08](docs/08-environment-and-migration.md) 的还原顺序可走通全流程。
6. `configs/opencode/` 保存全局指令栈三份文件的副本（`AGENTS.md` / `agents-md-rules.md` / `skills-usage.md`），改动后随仓库同步（方式见 [configs/README.md](configs/README.md)）。

## 维护

- 装新软件或换机后，按分类更新对应 `docs/` 文件；服务状态（端口、进程）以 `Get-Service`、`ss -lnt` 实测为准。
- 配置与经验类内容（06 / 07）随实际使用更新：改动配置或踩到新坑时同步补条目，别让文档漂移。

## License

MIT
