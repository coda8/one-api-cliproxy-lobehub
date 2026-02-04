# New API + CLIProxyAPI 一键部署

一个 Docker Compose 仓库，可一次性拉起 **New API** 与 **CLIProxyAPI** 两大核心服务，专注提供统一的 LLM API 聚合与高兼容代理能力。整个栈无需 `.env`，可以直接在 Portainer / Docker Compose 中复制粘贴部署。

## 服务概览

| 服务 | 说明 | 端口 |
|------|------|------|
| [New API](https://github.com/Calcium-Ion/new-api) | LLM API 聚合中转、线路管理、密钥路由 | 3000 |
| [CLIProxyAPI](https://github.com/router-for-me/CLIProxyAPI) | OpenAI / Gemini / Claude 等兼容 API 代理 | 8317 |

## 前置要求

- Docker 与 Docker Compose（或 `docker compose` v2）
- 建议资源：2 GB+ 内存、5 GB+ 磁盘
- 确保端口 **3000**、**8317** 未被占用

## 快速开始

1. 克隆本仓库：
   ```bash
   git clone https://github.com/coda8/new-api-cliproxy-lobehub.git
   cd new-api-cliproxy-lobehub
   ```
2. 启动所有服务：
   ```bash
   docker compose up -d
   ```
3. 首次进入 New API 控制台（http://服务器IP:3000）初始化并更改默认密码。

## 访问入口

- New API 控制台： http://localhost:3000
- CLIProxyAPI： http://localhost:8317
- CLIProxyAPI 管理界面： http://localhost:8317/management.html （登录密钥见下表）

## 默认账号 / 密钥

| 服务 | 用途 | 默认值 |
|------|------|--------|
| New API 控制台 | 管理后台 | 账号 `root` / 密码 `123456` |
| MySQL（New API 内部使用） | 应用连接 | `newapi` / `123456` |
| CLIProxyAPI 调用 | API Key | `llm-stack-default-key`（Authorization: Bearer ...） |
| CLIProxyAPI 管理界面 | 登录密钥 | `llm-stack-management-key` |

> ⚠️ 以上凭证均写在 `docker-compose.yml` 中，部署到生产环境务必修改。

## 配置说明

### New API
- 依赖 MySQL 与 Redis，容器启动后自动用内置 DSN.
- `SESSION_SECRET`、`SQL_DSN` 等可在 compose 内覆盖。

### CLIProxyAPI
- 配置通过 Docker `configs` 注入，文件内容位于 `docker-compose.yml` → `configs.cliproxy_config_yaml.content`。
- 需要自定义 API Key、远程管理密钥或新增上游渠道时，修改该片段后重启容器即可。

## 与前端/其他客户端对接

本栈只聚焦 API 网关，可将任何聊天前端（如 [Open WebUI](https://github.com/open-webui/open-webui) 或 [ChatGPT-Next-Web](https://github.com/ChatGPTNextWeb/ChatGPT-Next-Web)）指向 New API 或 CLIProxyAPI：

- **指向 New API**：HTTP 端点 `http://<你的服务器>:3000`，使用在控制台创建的 Token。
- **指向 CLIProxyAPI**：HTTP 端点 `http://<你的服务器>:8317/v1`，Key 为配置中的 `api-keys`。

## 故障排除

- **CLIProxyAPI 启动报 `config.yaml: is a directory`**：通常因曾经把 `./config.cliproxy.yaml` 以 bind 方式挂载，同时宿主上存在同名目录导致。改用本仓库提供的 configs 注入方式或删除该目录即可。
- **CLIProxyAPI 管理界面 404**：首次启动会从 GitHub 拉取静态资源，等待 ~30 秒再刷新。

## 日常维护

- 查看日志：`docker compose logs -f new-api` / `cli-proxy-api`
- 升级镜像：`docker compose pull && docker compose up -d`

## 停止与清理

```bash
docker compose down
# 同时清理数据卷：docker compose down -v
```

## License

本仓库未单独声明 License，沿用上游项目许可，请在生产环境中遵守相应条款。
