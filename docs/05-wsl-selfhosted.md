# WSL 自托管服务

WSL 里跑了 9 个 systemd 常驻服务（全部 `enabled`，随 WSL 启动自动拉起），承载对象存储、注册中心、内网导航与几个自建应用。Windows 侧通过本地端口转发可直接访问 `127.0.0.1:<端口>`；部分端口同时绑定到局域网地址供内网其他设备使用（以 `ss -lnt` / `netstat` 实测为准）。保活与端口转发的计划任务见 [08 环境与换机还原](08-environment-and-migration.md)。

## 服务清单

| 服务 | 版本 | 端口 | 位置 | 说明 |
| --- | --- | --- | --- | --- |
| Redis | 8.0.2 | 6379 | `redis-server.service` | 缓存 |
| Nacos | 3.2.4（standalone） | 8848 / 9848 / 9849，控制台 8080 | `/root/ai-infra/nacos/standalone/nacos-3.2.4` | 注册与配置中心 |
| RustFS | 1.0.0-rc.3 | 9000（S3）/ 9001（控制台） | `/usr/local/bin/rustfs` | 对象存储，**取代原 MinIO** |
| Caddy | 2.11.4 | 80 / 2015 / 8443 / 2019 | `caddy.service`，配置 `/etc/caddy/Caddyfile` | Web 服务器 / 反向代理，路由见下 |
| Homepage | — | 31257 | `/opt/homepage`（`pnpm start`） | 内网导航首页，服务入口维护在 `config/services.yaml` |
| Mibao 保险箱 | — | 18281 | `/opt/mibao`（独立系统用户 `mibao`） | 内部密码/凭据保险箱 |
| 打包工坊（Packing Plant） | — | 17293 | `/opt/packing-platform` | 打包平台 |
| 服务看板（Services Dashboard） | — | 18090 | `/opt/services-dashboard`，配置 `/etc/services-dashboard/config.yaml` | 服务发现与端口探活看板 |
| 文枢（WenShu） | Astro 7.3.2 ＋ Python | 8903 | `/opt/wenshu/app`（`python3 server.py`） | 文档站点与 `wenshu_*` 工具后端；`wenshu-backup.timer` 每日 03:30 备份 |
| 未识别 | — | 7848 | — | HTTP/2 cleartext（gRPC）在监听，归属未确认 |

> 7848 不在以上任何服务的已知端口内，且几处配置里都没有引用；换机时留意它是否需要重建（待确认）。

## Caddy 路由（`/etc/caddy/Caddyfile`）

| 入口 | 去向 | 说明 |
| --- | --- | --- |
| `:80` | `127.0.0.1:31257` | 内网导航（Homepage） |
| `:2015` | 静态文件服务：`root /mnt/d/ftp/upload`，`file_server browse` | 与 Windows 侧 FTP 服务共用上传目录 |
| `gitea.internal:8443` | 内网 Gitea 主机 `:3000`，`tls internal`（自签） | 给 OpenChamber/Git 提供 https 端点，链路细节见 [07 经验手册](07-playbook.md) |
| `:2019` | — | Caddy admin API（仅本机） |

## 运行环境

- WSL：3.0.3.0；发行版 Debian 13 (trixie)；`systemd=true`（`/etc/wsl.conf` 关键项见 [08](08-environment-and-migration.md)）。
- WSL 内运行时：Python 3.13.5、Node **v26.5.0**、pnpm 12.6.0、Go 1.26.5、fish —— 与 Windows 侧（Node 22.23.0）**不是同一版本**，跨侧跑构建脚本时注意。
- apt 手工安装的关键包：`caddy`、`maven`、`golang`、`git`、`fish`、`build-essential`。
- 代码与数据目录：`/home/mofeibai/code`（wisdom、xingtai-attendance-api 等）、`/home/mofeibai/sd-build`、`/root/ai-infra`（Nacos）。

## 与 Windows 的关系

| 事项 | 做法 |
| --- | --- |
| WSL 保活 | 计划任务 `WenShu_WSLKeepAlive` 长期持有一个会话，避免 WSL 空闲回收后服务不可达 |
| 端口转发刷新 | 计划任务 `WenShu_PortProxy` 定期重建端口转发（8903 等） |
| 自签 https 启动 | 计划任务 `WSL-CaddyGitea` 开机拉起 WSL 并启动 Caddy |
| 共享上传目录 | `D:\FTP\upload` ↔ `/mnt/d/ftp/upload`（Windows 侧另有 FTP 服务自启项） |
| 证书信任 | Caddy 的自签根证书**未导入** Windows 信任库，见 [08](08-environment-and-migration.md) |
