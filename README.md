# One-API + CLIProxyAPI + LobeHub 一键部署

一个 Docker Compose 仓库，用于**一次性**部署以下三个服务。**无需 .env**，便于在 Portainer 中直接拉取或粘贴 compose 部署。

| 服务 | 说明 | 端口 |
|------|------|------|
| [One-API](https://github.com/songquanpeng/one-api) | LLM API 统一管理与转发 | 3000 |
| [CLIProxyAPI](https://github.com/router-for-me/CLIProxyAPI) | OpenAI/Gemini/Claude 等兼容 API 代理 | 8317 |
| [LobeHub](https://github.com/lobehub/lobehub) | 开源 AI 聊天前端（含 S3、PostgreSQL、Redis） | 3210（Lobe）、9000（RustFS） |

由 [coda8](https://github.com/coda8/coda8) 根据 [xx025/xx025#4](https://github.com/xx025/xx025/issues/4) 创建。

## 前置要求

- Docker 与 Docker Compose（或 `docker compose` v2）
- 足够磁盘与内存（建议 4GB+ 内存，10GB+ 磁盘）

## 快速开始（无需 .env）

```bash
git clone https://github.com/coda8/one-api-cliproxy-lobehub.git
cd one-api-cliproxy-lobehub
docker compose up -d
```

**Portainer**：在 Stack 中选择「从 Git 仓库拉取」填入本仓库 URL，或复制 `docker-compose.yml` 内容粘贴创建 Stack，无需配置环境变量即可部署。生产环境建议在 compose 中修改默认密码（MySQL、PostgreSQL、RustFS、One-API SESSION_SECRET 等）。

## 访问

- One-API: http://localhost:3000  
- CLIProxyAPI: http://localhost:8317  
- LobeHub: http://localhost:3210  
- RustFS（S3）: http://localhost:9000  

## 配置说明

- **One-API**：首次访问 3000 端口完成初始化；数据库与 Redis 已内置。
- **CLIProxyAPI**：仓库内已带 `config.cliproxy.yaml`，可按需修改 `api-keys` 及各厂商登录，参见 [官方文档](https://help.router-for.me/docker/docker-compose)。
- **LobeHub**：PostgreSQL、Redis、RustFS 与 S3 桶已配置；如需改密码，请直接编辑 `docker-compose.yml` 中对应环境变量。

## 停止与清理

```bash
docker compose down
# 需同时删除数据卷时：docker compose down -v
```

## 相关链接

- [One-API](https://github.com/songquanpeng/one-api)
- [CLIProxyAPI](https://github.com/router-for-me/CLIProxyAPI) · [文档](https://help.router-for.me/)
- [LobeHub](https://github.com/lobehub/lobehub) · [自托管文档](https://lobehub.com/docs/self-hosting/platform/docker-compose)
