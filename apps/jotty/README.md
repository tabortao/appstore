一个用于管理清单和笔记的自托管应用。

[jotty·page](https://jotty.page) 是管理个人清单和笔记的轻量级替代方案。它部署极其简单，可将所有数据保存在您自己的服务器上，并允许您对笔记进行加密/解密，让您倍感安心。

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

