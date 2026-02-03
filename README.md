# New API + CLIProxyAPI + LobeHub 一键部署

一个 Docker Compose 仓库，用于**一次性**部署以下三个服务。**无需 .env**，便于在 Portainer 中直接拉取或粘贴 compose 部署。

| 服务 | 说明 | 端口 |
|------|------|------|
| [New API](https://github.com/Calcium-Ion/new-api) | LLM API 统一管理与转发（AI 模型聚合中转） | 3000 |
| [CLIProxyAPI](https://github.com/router-for-me/CLIProxyAPI) | OpenAI/Gemini/Claude 等兼容 API 代理 | 8317 |
| [LobeHub](https://github.com/lobehub/lobehub) | 开源 AI 聊天前端（含 S3、PostgreSQL、Redis） | 3210（Lobe）、9000（RustFS） |

由 [coda8](https://github.com/coda8/coda8) 根据 [xx025/xx025#4](https://github.com/xx025/xx025/issues/4) 创建。

## 前置要求

- Docker 与 Docker Compose（或 `docker compose` v2）
- 足够磁盘与内存（建议 4GB+ 内存，10GB+ 磁盘）

## 快速开始（单文件即可）

**只需复制一份 `docker-compose.yml`**，无需克隆仓库、无需 .env 或其他配置文件，在任意目录执行：

```bash
docker compose up -d
```

也可从本仓库复制 [docker-compose.yml](https://raw.githubusercontent.com/coda8/new-api-cliproxy-lobehub/main/docker-compose.yml) 后使用。**Portainer**：新建 Stack → 粘贴该文件内容或从上述 URL 拉取，即可部署。生产环境建议在 compose 中修改默认密码。

## 访问

- New API: http://localhost:3000  
- CLIProxyAPI: http://localhost:8317  
- LobeHub: http://localhost:3210  
- RustFS（S3）: http://localhost:9000  

## 默认账号与密码

| 服务 | 用途 | 用户名/Key | 密码/说明 |
|------|------|-------------|-----------|
| **New API 控制台** | 管理后台登录 | `root` | `123456`（首次登录后请立即修改） |
| **New API 用 MySQL** | 数据库（仅内部） | `root` | `NewAPI@stack` |
| **New API 用 MySQL** | 应用连接用 | `newapi` | `123456` |
| **CLIProxyAPI** | 调用 API 时的 Key | 请求头带 `Authorization: Bearer llm-stack-default-key` | 可在 compose 里改 init 容器生成的 config 或挂载自己的 config |
| **LobeHub** | 聊天前端 | 无预设管理员 | 首次访问可自注册账号 |
| **RustFS（S3）** | 控制台 / S3 兼容 | `admin` | `rustfs123` |
| **PostgreSQL（LobeHub 用）** | 数据库（仅内部） | `postgres` | `lobechat123` |

以上密码均在 `docker-compose.yml` 中可改；生产环境务必修改默认密码。

## 配置说明

- **New API**：首次访问 3000 端口完成初始化；数据库与 Redis 已内置。
- **CLIProxyAPI**：仓库内已带 `config.cliproxy.yaml`，可按需修改 `api-keys` 及各厂商登录，参见 [官方文档](https://help.router-for.me/docker/docker-compose)。
- **LobeHub**：PostgreSQL、Redis、RustFS 与 S3 桶已配置；如需改密码，请直接编辑 `docker-compose.yml` 中对应环境变量。

## 停止与清理

```bash
docker compose down
# 需同时删除数据卷时：docker compose down -v
```

## 相关链接

- [New API](https://github.com/Calcium-Ion/new-api) · [文档](https://docs.newapi.pro/)
- [CLIProxyAPI](https://github.com/router-for-me/CLIProxyAPI) · [文档](https://help.router-for.me/)
- [LobeHub](https://github.com/lobehub/lobehub) · [自托管文档](https://lobehub.com/docs/self-hosting/platform/docker-compose)
