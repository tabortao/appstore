# LX Music Sync Server

LX Music 数据同步服务端。本项目目前用于收藏列表数据同步，类似桌面版的数据同步服务，只不过它现在是一个独立版的服务，可以将其部署到服务器上使用。


## 环境要求

- Node.js 16+

## 使用方法


可以使用以下方式部署：

```yaml
services:
  lx-music-sync:
    image: lyswhut/lx-music-sync-server:v2.1.2
    container_name: lx-music-sync
    restart: unless-stopped
    ports:
      - "29527:9527"
    volumes:
      - ./data:/server/data
      - ./logs:/server/logs
    environment:
      - LX_USER_admin=user-admin    
      - LX_USER_mom=admin-paww
```

已发布到 Docker Hub 的镜像：<https://hub.docker.com/r/lyswhut/lx-music-sync-server>

也可以看此 Issue 提供的解决方案：<https://github.com/lyswhut/lx-music-sync-server/issues/4>
