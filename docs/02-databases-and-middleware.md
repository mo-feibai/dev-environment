# 数据库与中间件

## 服务端

| 组件 | 版本 | 运行位置 | 端口 | 状态 | 简介 | 链接 |
| --- | --- | --- | --- | --- | --- | --- |
| MySQL Server | 8.0.33 | Windows 服务 `MySQL80` | 3306 | 运行中 | 主数据库 | https://www.mysql.com/ |
| MySQL Installer | 1.6.15 | Windows | — | 已装 | MySQL 安装/升级器 | https://dev.mysql.com/downloads/installer/ |
| RabbitMQ | 4.3.4（scoop）+ Erlang 27.3.4.15 | Windows 服务 `RabbitMQ` | 5672 | 已装、未启动 | 消息队列 | https://www.rabbitmq.com/ |
| Elasticsearch | 9.1.3 | `D:\elasticsearch\elasticsearch-9.1.3-windows-x86_64` | 9200 | 已装、未启动 | 搜索与日志分析 | https://www.elastic.co/elasticsearch |
| 达梦数据库（DM） | — | — | — | **仅客户端，无本机服务** | 国产数据库（信创） | https://www.dameng.com/ |

> 说明：
>
> 1. 本机未安装达梦数据库服务端；只有达梦官方客户端 SQLark（见下表）。端口 5236 上监听的是 VMware NAT 组件。
> 2. RabbitMQ 现在由 scoop 管理（`D:\scoop\apps\rabbitmq` + scoop 的 Erlang，服务可执行文件指向 scoop 的 `erlsrv.exe`）；注册表里还留着旧版 3.13.7 的卸载条目（残留，勿据此判断版本）。
> 3. **Elasticsearch 未启动时端口 9200 被 cpolar 面板占用**（cpolar 默认面板端口即 9200）。要启动 ES 需先改 cpolar 面板端口或停掉 cpolar，否则端口冲突。
> 4. Redis / Nacos / RustFS / Caddy 等 WSL 侧服务已独立成篇：[05 WSL 自托管服务](05-wsl-selfhosted.md)。

## 数据库客户端与接口工具

| 软件 | 版本 | 简介 | 链接 |
| --- | --- | --- | --- |
| SQLark 百灵连接 | 3.9 | 达梦官方数据库开发/管理/迁移工具，支持 DM、Oracle、MySQL，含造数与迁移评估 | https://www.sqlark.com/ |
| Navicat Premium | 16.3.7 | 商业多数据库客户端 | https://www.navicat.com/ |
| DBX | 0.6.44 | 轻量跨平台数据库客户端，支持 90+ 数据库（含达梦），内置 AI 与 MCP Server | https://github.com/t8y2/dbx |
| nacos-cli | 1.0.2 | Nacos 命令行客户端（`%LOCALAPPDATA%\Programs\nacos-cli`，配套 `nacos-setup`） | https://nacos.io/ |
| Another Redis Desktop Manager | 1.7.4 | Redis 图形客户端 | https://github.com/qishibo/AnotherRedisDesktopManager |
| Apifox | 2.8.32 | 接口设计、调试、Mock、文档一体化 | https://apifox.com/ |
| Bruno | 4.2.1 | 离线 API 客户端，请求以文件保存、适合 Git 管理 | https://github.com/usebruno/bruno |
| MQTTX | 1.12.1 | MQTT 客户端（调试物联网/消息场景） | https://github.com/emqx/MQTTX |
