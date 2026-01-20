<p align="center">
  <img src="public/app-icons/logos/logo-animated.svg" alt="Jotty Logo" width="100"> 
  <br />
  <h1 align="center">jotty·page</h1><br/>
</p>

一个用于管理清单和笔记的自托管应用。

[jotty·page](https://jotty.page) 是管理个人清单和笔记的轻量级替代方案。它部署极其简单，可将所有数据保存在您自己的服务器上，并允许您对笔记进行加密/解密，让您倍感安心。

<p align="center"><i>原名 rwMarkable</i></p>

---

<p align="center">
  <a href="https://discord.gg/invite/mMuk2WzVZu">
    <img width="40" src="public/repo-images/discord.svg">
  </a>
  <a href="https://www.reddit.com/r/jotty">
    <img width="40" src="public/repo-images/reddit.svg">
  </a>
  <a href="https://t.me/jottypage">
    <img width="40" src="public/repo-images/telegram.svg">
  </a>
  <br />
  <br />
  <i>加入我们的社区</i>
  <br />
</p>

---

<br />

<div align="center">
  <p align="center">
    <em>简洁直观的界面，用于管理您的清单和任务。</em>
  </p>
  <img src="public/app-screenshots/notes-view-dark.png" alt="Notes Home View" width="400" style="border-radius: 8px; box-shadow: 0 4px 12px rgba(0,0,0,0.1);">

  <p align="center">
    <em>富文本笔记编辑器。</em>
  </p>
  <img src="public/app-screenshots/note-markdown.png" alt="Note Editor" width="400" style="border-radius: 8px; box-shadow: 0 4px 12px rgba(0,0,0,0.1); margin: 0 8px;">
</div>

## 快速导航

- [特性](#features)
- [快速入门](#getting-started)
  - [Docker Compose (推荐)](#docker-compose)
  - [初始设置](#initial-setup)
  - [本地开发 (不使用 Docker)](#local-development-without-docker)
- [数据存储](#data-storage)
- [版本方案](#versioning)
- [加密](#encryption)
- [API](#api)
- [快捷键](#shortcuts)
- [通过 OIDC 实现单点登录 (SSO)](#single-sign-on-sso-with-oidc)
- [多重身份验证 (MFA)](#multi-factor-authentication)
- [翻译](#translations)
- [自定义主题和表情符号](#custom-themes-and-emojis)

<p align="center">
  <br />
  <a href="https://www.buymeacoffee.com/fccview">
    <img src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" alt="Buy me a coffee" width="150">
  </a>
</p>

<a id="features"></a>

## 特性

- **清单：** 创建支持拖拽排序、进度条和分类的任务列表。支持简单清单以及带有看板和时间追踪的高级任务项目。
- **富文本笔记：** 简洁的所见即所得 (WYSIWYG) 笔记编辑器，由 TipTap 驱动，支持完整的 Markdown 和语法高亮。
- **共享：** 与您实例上的其他用户共享清单或笔记，支持通过共享链接进行公共分享。
- **基于文件：** 无需数据库！所有内容均以简单的 Markdown 和 JSON 文件存储在单个数据目录中。
- **用户管理：** 提供管理员面板，用于创建和管理用户账户及会话跟踪。
- **可定制：** 内置 14 种主题，并支持自定义主题、自定义表情符号和图标。
- **加密：** 全面支持 PGP 加密，详情请参阅 [howto/ENCRYPTION.md](howto/ENCRYPTION.md)。
- **API 访问：** 通过带有身份验证的 REST API 以编程方式访问您的清单和笔记。

<a id="getting-started"></a>

## 快速入门

我推荐使用 Docker 运行 `jotty·page`。您也可以使用：

- 适用于 Proxmox VE 的 [Proxmox 社区脚本](https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/ct/jotty.sh)
- 适用于 Unraid Community Applications 的 [Unraid 模板](howto/UNRAID.md)

<a id="docker-compose"></a>

### Docker Compose (推荐)

1.  创建一个 `docker-compose.yml` 文件：

    **📖 有关高级设置以及 Docker Compose 文件工作原理和变量含义的更多信息，请阅读 [howto/DOCKER.md](howto/DOCKER.md)**

    ```yaml
    services:
      jotty:
        image: ghcr.io/fccview/jotty:latest
        container_name: jotty
        user: "1000:1000"
        ports:
          - "1122:3000"
        volumes:
          - ./data:/app/data:rw
          - ./config:/app/config:rw
          - ./cache:/app/.next/cache:rw
        restart: unless-stopped
        environment:
          - NODE_ENV=production
    ```

2.  创建数据目录并设置权限：

    ```bash
    mkdir -p config data/users data/checklists data/notes data/sharing data/encryption cache
    sudo chown -R 1000:1000 data/
    sudo chown -R 1000:1000 config/
    sudo chown -R 1000:1000 cache/
    ```

    **注意：** 缓存 (cache) 目录是可选的。如果您不需要持久化缓存，可以注释掉 `docker-compose.yml` 中的缓存卷行。

3.  启动容器：

    ```bash
    docker compose up -d
    ```

应用程序将运行在 `http://localhost:1122`。

<a id="initial-setup"></a>

### 初始设置

首次访问时，如果禁用了 SSO，您将被重定向到 `/auth/setup` 以创建管理员账户；否则，系统将提示您通过所选的 SSO 提供商登录。

完成后，您就可以开始了！默认情况下，第一个用户将成为管理员。

<a id="local-development-without-docker"></a>

### 本地开发 (不使用 Docker)

如果您想在本地进行开发运行：

1.  **克隆并安装：**
    ```bash
    git clone <repository-url>
    cd checklist
    yarn install
    ```
2.  **运行开发服务器：**
    ```bash
    yarn dev
    ```
    应用将运行在 `http://localhost:3000`。

<a id="data-storage"></a>

## 数据存储

`jotty·page` 在 `data/` 目录中使用简单的基于文件的存储系统。

- `data/checklists/`: 以 `.md` 文件存储所有清单。
- `data/notes/`: 以 `.md` 文件存储所有笔记。
- `data/users/`: 包含 `users.json` 和 `sessions.json`。
- `data/sharing/`: 包含 `shared-items.json`。
- `data/encryption/`: 包含所有用户的公钥/私钥。

**请务必备份 `data` 目录！**

<a id="versioning"></a>

## 版本方案

本项目使用 `[稳定版].[功能版].[修复版]` 的版本方案，而非严格的 [SemVer](https://semver.org/)。作为一款产品（而非包），这种格式更符合我的特定发布周期。

我的版本格式为 `1.10.1`，分解如下：

- **`1.x.x` (稳定版):** `1` 代表当前的稳定世代。只有在产品发生完全重写、根本性转变或严重的破坏性变更时，我才会更改此数字（例如升级到 `2.0.0`）。

- **`x.10.x` (功能版):** 这是主要的发布编号。我会针对新功能、代码重构或重大更改增加此数字（例如 `1.9.0` -> `1.10.0`）。这相当于 SemVer 的 `MINOR` 更新。

- **`x.x.1` (修复版):** 此数字仅针对热修复、仅包含 bug 修复的发布以及非常微小的功能发布而增加（例如 `1.10.0` -> `1.10.1`）。这相当于 SemVer 的 `PATCH` 更新。

### 关于“破坏性”变更的说明

**功能版** (如 `1.10.0`) 可能会包含重大的后端或数据结构更改。发生这种情况时，**我将始终提供自动迁移脚本**，该脚本会在首次启动时运行，以无缝更新您的数据。

由于迁移是自动的，我不认为这是需要 `2.0.0` 版本的“破坏性”变更。

我始终会在发布说明中详细说明这些迁移。我 *强烈建议* 您在进行任何功能更新之前 **备份您的数据**，以防万一。

<a id="supported-markdown"></a>

## 支持的 MARKDOWN

`jotty·page` 支持 GitHub Flavored Markdown (GFM) 以及一些用于复杂功能的自定义语法。

📖 **有关完整的 MARKDOWN 文档，请参阅 [howto/MARKDOWN.md](howto/MARKDOWN.md)**

<a id="encryption"></a>

## 加密

`jotty·page` 使用行业标准的 PGP 加密。

📖 **有关完整的加密文档，请参阅 [howto/ENCRYPTION.md](howto/ENCRYPTION.md)**

<a id="api"></a>

## API

`jotty·page` 包含一个 REST API，用于以编程方式访问您的清单和笔记。这非常适合：

- **自动化：** 从外部系统创建任务
- **集成：** 与其他工具和服务连接
- **脚本：** 自动化重复性任务
- **仪表板：** 构建自定义界面

📖 **有关完整的 API 文档，请参阅 [howto/API.md](howto/API.md)**

<a id="shortcuts"></a>

## 快捷键

`jotty·page` 支持广泛的键盘快捷键，帮助您更高效地导航和编辑，而无需离开键盘。它们分为两大类：在应用任何地方都有效的全局快捷键，以及在编写笔记时有效的编辑器专用快捷键。

📖 **有关完整的快捷键文档，请参阅 [howto/SHORTCUTS.md](howto/SHORTCUTS.md)**

<a id="single-sign-on-sso-with-oidc"></a>

## 通过 OIDC 实现单点登录 (SSO)

`jotty·page` 支持任何 OIDC 提供商 (Authentik, Auth0, Keycloak, Okta, Google, EntraID 等)。

📖 **有关完整的 SSO 文档，请参阅 [howto/SSO.md](howto/SSO.md)**

<a id="multi-factor-authentication"></a>

## 多重身份验证 (MFA)

`jotty·page` 支持 MFA，需要在 设置 -> 个人资料 中启用。

📖 **有关完整的 MFA 文档，请参阅 [howto/MFA.md](howto/MFA.md)**

<a id="translations"></a>



