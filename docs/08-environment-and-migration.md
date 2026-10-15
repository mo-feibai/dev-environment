# 环境与换机还原

机器级环境：计划任务与自启、环境变量、目录布局与备份、镜像源、主机信息、证书信任。**自启任务先看**——漏掉它们，WSL 里的服务会全部不可达。

## 计划任务与自启

### 关键：WSL 相关计划任务

| 任务 | 说明 |
| --- | --- |
| `WenShu_PortProxy` | 保活 WSL 服务并刷新端口转发（8903 等）；登录时触发，最高权限 |
| `WenShu_WSLKeepAlive` | 长期持有一个 WSL 会话，避免空闲回收后服务不可达（无执行时限） |
| `WSL-CaddyGitea` | 开机拉起 WSL Debian 并启动 Caddy（`gitea.internal:8443`），隐藏运行 |

### 其他计划任务（非系统 / 厂商类）

| 任务 | 用途 |
| --- | --- |
| `EcoPasteAdmin` | 剪贴板工具的管理员实例 |
| `memreductTask` | 内存清理 |
| `Snipaste (Run As Admin)` | 截图工具的管理员实例 |
| `SnowShot-…` | 截图工具（常驻） |
| `WallpaperToolLockScreen` | 锁屏壁纸 |
| `WSLDashboardTask` | WSL Dashboard 相关 |
| `Autorun for Administrator` | 登录自动运行 |

> 厂商自动更新（Edge / Google / WPS / Zen）与系统内置任务未列；授权类任务按仓库规则不列。

### 开机启动项

| 位置 | 条目 |
| --- | --- |
| HKCU `…\CurrentVersion\Run` | 花生壳（NewPeanuthull）、ApifoxAppAgent、Mem Reduct、EcoPaste、WSLDashboard、WallpaperTool、UU远程（GameViewer）、Flow Launcher、Rayburst、`D:\Clash\fgtrs\X5mR4IZW.exe`（随机名，来源待确认） |
| HKLM `…\CurrentVersion\Run` | Realtek 音频面板、小狼毫（WeaselServer）、Everything、WebVPN（Array VPN）、MotionPro MPInit |
| 启动文件夹 | `FTP Server.lnk`、`Snipaste.lnk` |

## 环境变量与 PATH

### 机器级

| 变量 | 值 | 用途 |
| --- | --- | --- |
| `JAVA_HOME` / `JAVA_HOME17` | `E:\Develop\Java\jdk-17` | 主 JDK |
| `JAVA_HOME1.8` | `E:\Develop\Java\jdk1.8.0_181` | 旧项目 |
| `JAVA_HOME11` | `E:\Develop\Java\jdk-11` | 中间版本 |
| `MAVEN_HOME` / `MAVEN_HOME_388` | `E:\Develop\apache-maven-3.8.8` | Maven 主版本 |
| `MAVEN_HOME_361` | `E:\Develop\apache-maven-3.6.1` | 旧项目 |
| `DM_HOME` | `C:\Users\Administrator\DAMENGDRIVER\dameng_odbc_win` | 达梦 ODBC 驱动 |
| `CPOLAR_HOME` | `D:\cpolar\` | 内网穿透 |
| `RABBITMQ_HOME` / `ERLANG_HOME` | `D:\scoop\apps\rabbitmq\current` / `…\erlang\current` | 消息队列 |
| `NVM_HOME` / `NVM_SYMLINK` | `D:\nvm_stupe\nvm` / `D:\nvm_stupe\nodejs` | Node 版本管理 |
| `CLASSPATH` | `.;E:\Develop\Java\jdk-17\lib;…\lib\tools.jar` | `tools.jar` 在 JDK 9+ 已移除，属死条目（见 [07](07-playbook.md)） |

**PATH（机器级）要点**：JDK 17、Maven 3.8.8、MySQL、GitHub CLI、Git、cloudflared、Tailscale、WSL、Python、.NET、scoop shims、VMware。其中 TortoiseSVN 那一项**写错了**（`D:\Tor;oiseSVN\bin`），修复建议见 [07](07-playbook.md)。

### 用户级

| 变量 | 值 / 要点 |
| --- | --- |
| `SCOOP` | `D:\scoop` |
| `RABBITMQ_SERVER` | `D:\scoop\apps\rabbitmq\current` |
| `UV_CACHE_DIR` / `UV_PYTHON_BIN_DIR` | `D:\scoop\persist\uv\…`（uv 安装失败，仅残留） |
| `MINIO_ROOT_USER` / `MINIO_ROOT_PASSWORD` | MinIO 时代残留，无消费方（见 [07](07-playbook.md)） |
| `IntelliJ IDEA` / `WebStorm` | IDE 的 bin 目录（命令行启动） |
| 一批 `*_VM_OPTIONS` / `JETBRAINS_*` | 指向第三方包目录，属授权类，不展开 |

**PATH（用户级）**：DBX、uv shims、mingw、`go\bin`、scoop shims、nvm、VS Code、WebStorm、IDEA、cpolar、Python、nacos-cli / nacos-setup。

## 目录布局与备份

| 位置 | 内容 |
| --- | --- |
| `E:\work` | `dev-environment`（本仓库）、`haihong`、`safety-smart-app`、`test`、`tongsheng`、`utils` |
| `E:\project-docs` | 各项目文档：`embankment`、`plat_jk`、`project-packing-platform`、`services-dashboard`、`sites-office`、`szh`、`wisdom`、`wisdom_liangshan`、`xingtai`、`xingtai-smart-store-api`、`zhj-sites-api`；接口测试统一落 `<项目>\api-tests\` |
| `E:\home` | `dikou`、`ruoyi`、`wisdom`、`xingtai`（历史代码） |
| `E:\TS` / `E:\codegen` / `E:\notes` / `E:\logs` | 整站导出、代码生成、笔记、日志 |
| `D:\` | 工具与中间件（Git、scoop、nvm_stupe、JetBrains、navicat、FTP、ZKTeco…） |
| WSL | `/home/mofeibai/{code,go,sd-build}`、`/root/ai-infra`、`/opt/*`（自建服务）、`/var/lib/*`（服务数据） |
| `~/.config/opencode`、`~/.config/openchamber`、`~/.agents`、`~/.config/cortexkit` | AI 环境配置（含凭据，不入库） |

**备份现状**：只有文枢有自动备份（`wenshu-backup.timer` 每日 03:30 → `/opt/wenshu/app/backup.sh`）；未见其他自动备份任务，`E:\project-docs`、WSL 服务数据与项目源码需自行打包。

## 镜像源与仓库位置

| 工具 | 配置文件 | 值 |
| --- | --- | --- |
| npm / pnpm | `npm config` | `https://registry.npmmirror.com/` |
| Maven | `E:\Develop\apache-maven-3.8.8\conf\settings.xml` | aliyun mirror；本地仓库 `localRepository` 指向 `…\apache-maven-3.8.8\mvn_repo`（非默认 `~/.m2`） |
| pip | — | 未配置（默认 PyPI） |
| apt（WSL） | `/etc/apt/sources.list*` | debian 官方 + security、nodesource、caddy(cloudsmith)、adoptium |

## 主机信息

| 项 | 值 |
| --- | --- |
| 主机名 | `PC-202410151925` |
| 系统 | Windows 10 专业版（10.0.19045） |
| 主板 / CPU | Gigabyte B760M D2H DDR4 / Intel i5-13400F（10 核 16 线程） |
| 内存 | 16 GB |
| 显卡 | NVIDIA GTX 1650（驱动 610.88）；另有向日葵与 UU远程的虚拟显示器适配器 |
| 磁盘 | C: 200 GB（可用 71）/ D: 366 GB（可用 206）/ E: 365 GB（可用 313） |

## 证书与信任

- 系统根证书里存在 **Array Networks Localhost Root CA**（随 MotionPro VPN 安装，企业外网准入组件）。
- **Caddy 的自签根证书未导入系统信任库**：访问 `https://gitea.internal:8443` 靠仓库级 `http.sslVerify=false` + 凭据管理器（链路见 [07](07-playbook.md)）。
- 换机两条路：把 Caddy 根证书导入「受信任的根证书颁发机构」，或重做 `sslVerify` / 凭据设置。

## 换机还原顺序

1. **系统与驱动**：Windows → NVIDIA / Realtek 驱动 → WSL 2 与 Debian（`/etc/wsl.conf` 按 [07 第 12 条](07-playbook.md)）。
2. **包管理与运行时**：scoop（`D:\scoop`，按 [03](03-dev-toolchain.md) 的包清单恢复）、nvm + Node、Python、Go、Bun。
3. **工具链**：JDK 8 / 11 / 17 / 25、Maven（含 `conf\settings.xml` 与 `mvn_repo`）、Git、gh、TortoiseSVN、IDE。
4. **环境变量**：按上表重建；PATH 里两处坏条目**别照抄**。
5. **WSL 服务**：恢复 `/opt/*`、`/root/ai-infra`、systemd 单元与 `EnvironmentFile`，再恢复服务数据。
6. **网络与证书**：hosts（`gitea.internal` → `::1`）、系统代理与 ProxyOverride、git `insteadOf` 规则、Caddy 根证书或 `sslVerify`。
7. **计划任务与自启**：三个 WSL 关键任务 + 上表启动项。
8. **AI 环境**：按 [06 换机重建顺序](06-configs-and-agents.md) 恢复 OpenCode / OpenChamber 与 Skills。
9. **校验**：`Get-Service`、WSL 内 `ss -lnt`、`curl 127.0.0.1:<端口>` 逐个服务；`git ls-remote` 验证 Gitea 链路。
