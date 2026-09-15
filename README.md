# dev-environment

本机（Windows 开发机）的软件环境清单与配置说明，用于换机、重装或交接时快速还原。

## 环境概览

| 项 | 值 |
| --- | --- |
| 主机用途 | Java 后端（Spring Boot）+ MySQL / 达梦 + WSL 常驻服务 |
| 工作盘 | `D:\`（工具与中间件）、`E:\`（JDK / Maven / work）、WSL Debian |
| 包管理 | scoop、winget、nvm-windows |
| 编辑器/IDE | IntelliJ IDEA、WebStorm、VS Code、Lithe |
| AI 编码环境 | OpenChamber + OpenCode（插件、Skills、MCP） |

## 目录

- [OpenCode / OpenChamber 生态](docs/01-opencode-ecosystem.md) —— 应用、插件、MCP、23 个 Skills
- [数据库与中间件](docs/02-databases-and-middleware.md) —— MySQL、达梦、Redis、Nacos、MinIO、Caddy、RabbitMQ、Elasticsearch
- [开发工具链](docs/03-dev-toolchain.md) —— 运行时、构建工具、JDK、IDE
- [客户端与桌面工具](docs/04-tools-and-apps.md) —— 数据库客户端、API 工具、终端、远程、桌面效率

## 说明

1. 版本与运行状态采集于 **2026-09-15**。
2. 本仓库**不包含**任何密钥、token、内网 IP、账号与凭据信息。
3. 企业合规类（安全认证、终端管控）与授权类工具未列入本清单。
4. 表内链接优先使用官网，其次 GitHub 仓库。

## 维护

换机或装新软件后，按分类更新对应 `docs/` 文件即可；运行状态（端口、服务）以 `Get-Service` 与 `ss -lnt` 实测为准。

## License

MIT
