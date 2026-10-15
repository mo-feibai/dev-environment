# 开发工具链

## 运行时与包管理

| 软件 | 版本 | 位置 | 简介 | 链接 |
| --- | --- | --- | --- | --- |
| Node.js | 22.23.0 | `D:\nvm_stupe\nodejs` | JS 运行时（经 nvm-windows 管理） | https://nodejs.org/ |
| NVM for Windows | 1.2.2 | `D:\nvm_stupe\nvm` | Node 版本管理器 | https://github.com/coreybutler/nvm-windows |
| npm | 10.9.8 | — | Node 包管理器（registry 指向 npmmirror） | https://www.npmjs.com/ |
| corepack | 0.34.6 | — | 包管理器版本代理（yarn/pnpm） | https://github.com/nodejs/corepack |
| pnpm | 11.22.0 | — | 快速、省空间的包管理器 | https://pnpm.io/ |
| Bun | 1.4.2 | 全局 npm 包 | 一体化 JS 运行时/包管理器 | https://bun.sh/ |
| Python | 3.14.3 | `%LOCALAPPDATA%\Python\pythoncore-3.14-64` | Python 运行时 | https://www.python.org/ |
| pip | 26.1.1 | — | Python 包管理器（未配镜像） | https://pip.pypa.io/ |
| Go | 1.26.5 | scoop shim | Go 工具链 | https://go.dev/ |
| Scoop | v0.6.0 | `D:\scoop`（bucket：main、extras） | Windows 命令行包管理器 | https://scoop.sh/ |
| Windows Subsystem for Linux | 3.0.3.0 | 发行版：Debian 13 (trixie) | WSL2 运行环境（承载 9 个自托管服务，见 [05](05-wsl-selfhosted.md)） | https://github.com/microsoft/WSL |

### Scoop 包（`scoop list`）

| 包 | 版本 | 用途 |
| --- | --- | --- |
| `7zip` | 26.02 | 压缩/解压（与图形安装的 7-Zip 24.09 并存） |
| `erlang` | 27.3.4.15 | RabbitMQ 运行时 |
| `ffmpeg` | 9.0.2 | 音视频处理 |
| `go` | 1.26.5 | Go 工具链 |
| `hugo-extended` | 0.165.0 | 静态站点生成 |
| `memreduct` | 3.5.2 | 内存清理（开机自启） |
| `mingw` | 16.1.0-rt_v14-rev1 | Windows GCC 工具链 |
| `opencode` | 1.18.35 | OpenCode CLI（v1，与 OpenChamber 内置的 v2 CLI 并存） |
| `rabbitmq` | 4.3.4 | 消息队列 |
| `vcredist2022` | 14.51.36247.0 | VC++ 运行库 |
| `uv` | — | **安装失败**（`D:\scoop\persist\uv\` 留了 shims/cache 目录，PATH 里还引用着） |

## Java 与构建

| 软件 | 版本 | 位置 | 简介 | 链接 |
| --- | --- | --- | --- | --- |
| JDK（主用） | 17.0.1 | `E:\Develop\Java\jdk-17`（`JAVA_HOME`） | Spring Boot 开发主 JDK | https://www.oracle.com/java/technologies/downloads/ |
| JDK 8 | 8u181 | `E:\Develop\Java\jdk1.8.0_181` | 兼容旧项目 | https://www.oracle.com/java/technologies/downloads/ |
| JDK 11 | 11.0.15.1 | `E:\Develop\Java\jdk-11`（`JAVA_HOME11`） | 中间版本 | https://www.oracle.com/java/technologies/downloads/ |
| JDK 25 | 25.0.2 | `E:\jdk-25.0.2` | 尝鲜版本 | https://jdk.java.net/ |
| Apache Maven | 3.8.8 | `E:\Develop\apache-maven-3.8.8` | Java 构建/依赖管理（另有 3.6.1 旧版） | https://maven.apache.org/ |

> Maven 本地仓库被配置在安装目录内：`E:\Develop\apache-maven-3.8.8\mvn_repo`（非默认 `~/.m2`），升级或重装 Maven 时注意保留/迁移。镜像源见 [08 环境与换机还原](08-environment-and-migration.md)。

## 版本控制

| 软件 | 版本 | 简介 | 链接 |
| --- | --- | --- | --- |
| Git | 2.55.0 | 版本控制（`D:\Git`） | https://git-scm.com/ |
| GitHub CLI | 2.100.0 | GitHub 命令行（仓库、PR、Actions） | https://github.com/cli/cli |
| TortoiseSVN | 1.14.9 | Windows 资源管理器集成的 SVN 客户端 | https://tortoisesvn.net/ |
| Inno Setup | 7.1.0 | Windows 安装包制作工具 | https://jrsoftware.org/isinfo.php |

## IDE 与编辑器

| 软件 | 版本 | 简介 | 链接 |
| --- | --- | --- | --- |
| IntelliJ IDEA | 2026.2.3 | Java 主力 IDE（`D:\JetBrains\`；`E:\Develop\IntelliJ IDEA 2025.1.3` 是旧版残留目录） | https://www.jetbrains.com/idea/ |
| WebStorm | 2025.1.1 | 前端/Node IDE | https://www.jetbrains.com/webstorm/ |
| Visual Studio Code | 1.100.2 | 通用编辑器（52 个扩展） | https://code.visualstudio.com/ |
| Lithe | 0.5.1 | 轻量跨平台 IDE（面向 AI 时代，带按需语言服务） | https://github.com/1lck/Lithe-IDEA |
