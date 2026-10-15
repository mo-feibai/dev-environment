# 经验手册

本机踩过、且已验证结论的坑与做法。换机或复刻环境时按此避雷。

## A. OpenCode 与模型层

1. **reasoning 模型必须配 variant**：magic-context 的 historian/dreamer 这类内部 agent 用 reasoning 模型时，必须在配置里指定 variant（如 `low`）；否则思考会烧满 API 的输出上限（默认 16,384），正文为空（`finish_reason=length`），historian 报 `ran out of output budget while reasoning`、历史压缩卡死。variant 必须写模型真实存在的名字，否则宿主报 `provider.no-route: Variant unavailable`（可用 `opencode models <provider> --verbose` 查）。

2. **magic-context 的配置与生效时机**：
   - 配置唯一读取路径是 `~/.config/cortexkit/magic-context.jsonc`（旧的 `~/.config/opencode/magic-context.*` 已被启动时迁移）。
   - model/variant 必须写在 harness 子块内（`historian.opencode.model` / `dreamer.opencode.model`）；裸 `historian.model` 不被读取，会**静默永不运行**。
   - historian/dreamer 发送的 model+variant 取自 opencode 进程的**启动快照**：改完配置必须重启 opencode 才生效，日志里的 `config reloaded` 不能当作生效证据。

3. **免费模型不能喂给内部 agent**：`opencode/*` 的免费模型不能给 magic-context 的 historian/dreamer 用——Zen 网关按**请求体**鉴权，内部无 bash 工具的 agent 一律 403 `FreeTierError`，无可配置绕过。本机改用 `agentrouter/deepseek-v4-flash`。

4. **AgentRouter（DeepSeek 通道）会改写工具 schema**：缺 `required` 字段的对象型 schema 会被上游改写成 `required: null`，DeepSeek 严格校验拒绝（`Invalid schema for function 'ctx_expand': null is not of type "array"`）。本机用 `plugins/agentrouter-tool-schema.ts` 在模型请求组装阶段补 `required: []` 规避。

5. **v2 插件工具注册契约**：`execute` 必须返回 `{ content: string }`（裸字符串或 `{ output }` 都报错）；每个工具必须带 `name`（缺了会在宿主触发 `e.name.replace` TypeError，整个插件被判 disabled 并反复重载）；本地插件只能以**目录**形式登记（`file:///…/插件目录`），不能指向单个文件或子路径入口。

6. **凭证分两库**：新引擎（v2）的登录凭证在 `~/.local/share/opencode/opencode.db` 的 `credential` 表；`~/.local/share/opencode/auth.json` 是 v1/CLI 旧库。清理或替换某 provider 的登录必须动 DB 里的行，只清 `auth.json` 对界面无效。

## B. 网络、证书与代理

7. **自签 https 反代内网 Gitea 的完整链**（本机现状，OpenChamber 能推代码的前提）：
   1. `git config --global url."https://gitea.internal:8443/".insteadOf "http://<内网 Gitea 地址>/"` —— 把 http 远端重写成 https；
   2. Windows `hosts` 里 `gitea.internal` 指向 **`::1`**（端口转发只绑 IPv6 回环，写 `127.0.0.1` 连不上）；
   3. WSL 内 Caddy 监听 8443（`tls internal` 自签）反代到真实 Gitea 主机；
   4. 自签根证书未导入 Windows 信任库，靠仓库级 `http.sslVerify=false` + Git 凭据管理器通过。
   起因是 OpenChamber ≥2.1.1 的 Git 网络操作只放行 `https:`/`ssh:` 端点（见第 10 条）。

8. **GCM 走 Windows 系统代理（WinINET）**：Git Credential Manager 的 HTTP 请求受 HKCU `Internet Settings\ProxyOverride` 控制。`gitea.internal` 必须在绕过列表里，否则经代理转发失败 → OAuth token 刷新失败 → 凭据被清空、Git 认证崩溃。诊断命令：`[System.Net.WebRequest]::DefaultWebProxy.GetProxy('https://gitea.internal:8443/')`。注意 `git.exe` / `curl` 不走 WinINET，所以表象是「git 手测正常、GCM 静默失败」。

9. **`git remote get-url` 会应用 `insteadOf`**：OpenChamber 读远端用的就是它——「origin 存 http + 全局重写 http→https」能通过端点校验；反向（origin 存 https + 重写 https→http）会被拒。方向错了方案就不成立。

10. **OpenChamber ≥2.1.1 只放行 https/ssh 远端**：校验分**两层**，补丁必须同时打两处，只改 app.asar 无效——(1) 服务端 `app.asar` 内的 `assertSafeExistingEndpoint` 系列（`network-operation-plan.js` / `-registry.js` / `-storage.js`、`worktree-bootstrap-storage.js` 等）；(2) 前端 `web-dist/assets/index-*.js` 里自带的压缩版校验（zod refine，报错形如 `target.fetch.endpoint.displayUrl: Invalid input`）。≤2.1.0 无此限制。

11. **本机镜像源**：npm / pnpm → `https://registry.npmmirror.com/`；Maven → aliyun mirror；apt（WSL）→ debian + nodesource + caddy(cloudsmith) + adoptium；pip 未配置。

## C. Windows 与 WSL

12. **`/etc/wsl.conf` 关键项**：`systemd=true`（自建服务的前提）、`interop.enabled=false` 与 `appendWindowsPath=false`（隔离 Windows PATH）、`generateResolvConf=false`、自定义 `hostname`、`time.useWindowsTimezone=true`。

13. **WSL systemd 服务模板**：独立系统用户 + `EnvironmentFile=/etc/<服务>/env` + `Restart=always` + `WorkingDirectory` + 日志 append 到 `/var/log/<服务>/`；本机 9 个自托管服务都是这个形状（见 [05](05-wsl-selfhosted.md)）。

14. **Bun 1.4.2 的 `bun:sqlite` 坑**：显式传 `{ readonly: false }` 或 `{ create: false }` 给 `new Database(path, opts)` 会构造时抛 `SQLITE_MISUSE`（errno=21）。读写用默认参数 `new Database(path)`，只读才传 `{ readonly: true }`。

15. **WSL 端口转发常只绑 IPv6**：Windows 侧看到的监听可能只有 `[::1]`，所以 hosts 与访问地址要优先用 IPv6 回环（见第 7 条）。

## D. 操作纪律

16. **破坏性维护先在正文给命令、由用户执行**：会中断会话或不可逆的操作（SQLite VACUUM、关闭数据库连接、停/重启托管服务）先把完整可复制命令连同影响说明一次给出，请用户在自己的终端执行；不要替用户执行可能切断交互通道的动作。

17. **换机不搬凭据**：所有 token / 口令 / API key 在新机重新登录或生成（见 [06](06-configs-and-agents.md) 的重建顺序）。

## 附：本机现状坑（含修复建议）

| 现状 | 影响 | 建议 |
| --- | --- | --- |
| 机器 PATH 含 `D:\Tor;oiseSVN\bin`（分号写错） | 被拆成 `D:\Tor` 与 `oiseSVN\bin` 两个死路径，TortoiseSVN 命令行工具实际不在 PATH | 改为 `D:\TortoiseSVN\bin` |
| PATH 含 `E:\Develop\IntelliJ IDEA 2025.1.3\bin`、`C:\Users\Administrator\scoop\shims` | 指向已不存在的目录 | IDEA 项改指当前版本目录；空的用户级 scoop shims 目录及 PATH 项一并删除 |
| 用户变量 `MINIO_ROOT_USER` / `MINIO_ROOT_PASSWORD` | MinIO 已被 RustFS 取代，无消费方 | 确认无脚本引用后删除 |
| 同时存在 `D:\scoop`（生效）与空的 `C:\Users\Administrator\scoop` | 两个 scoop 根，易误判包安装位置 | 删除空目录及其 PATH 项 |
| `cli.json` 钉 magic-context **0.42.6**，`tui.jsonc` 用 **@latest** | TUI 与 CLI 可能加载不同版本 | 统一为同一个明确版本号 |
| `~/.config/opencode` 混入 30+ 个调试 `.py` 与 20 张 `shot-*.png` | 配置目录噪音，易误入备份 | 移到专门的 scratch 目录 |
| Elasticsearch 未启动，但 9200 被 cpolar 面板占用 | 直接启动 ES 会端口冲突 | 改 cpolar 面板端口或拆开 ES 端口 |
| Caddy 自签根证书未导入 Windows 信任库 | 换机后 https 链路（内网 Gitea）会失败 | 导入 Caddy 根证书，或重做仓库级 `sslVerify=false` + 凭据 |
| 开机启动项里 `D:\Clash\fgtrs\` 下有一个随机命名的可执行文件 | 来源不明、随机名，换机必漏 | 确认归属（疑似代理工具附带），无用的清掉 |
| 机器级 `CLASSPATH` 指向 `E:\Develop\Java\jdk-17\lib\tools.jar` | `tools.jar` 在 JDK 9+ 已移除，属死条目；个别老构建脚本仍依赖 CLASSPATH 时会踩 | 清理该条目，或按需改为不带 `tools.jar` 的 classpath |
