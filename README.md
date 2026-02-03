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
- 以下端口未被占用：**3000**（New API）、**8317**（CLIProxyAPI）、**9000/9001/3210**（LobeHub/RustFS）。若端口冲突，请修改 `docker-compose.yml` 中对应端口的 `published` 值。

## 快速开始

**复制一份 `docker-compose.yml`** 即可部署（配置由 compose 内 configs 内联注入），无需 .env 或额外文件。在任意目录执行：

```bash
docker compose up -d
```

**CLIProxyAPI 管理界面**（http://服务器:8317/management.html）：若刚启动时打开为 404，等约 30 秒再试（服务会从 GitHub 拉取面板资源）。登录管理密钥：`llm-stack-management-key`。生产环境请在 compose 的 `configs.cliproxy_config_yaml.content` 中修改默认 api-keys 与 `remote-management.secret-key`。

## 访问

- New API: http://localhost:3000  
- CLIProxyAPI API: http://localhost:8317  
- **CLIProxyAPI 管理界面**: http://localhost:8317/management.html（管理密钥见下表）  
- LobeHub: http://localhost:3210  
- RustFS（S3）: http://localhost:9000  

## 默认账号与密码

| 服务 | 用途 | 用户名/Key | 密码/说明 |
|------|------|-------------|-----------|
| **New API 控制台** | 管理后台登录 | `root` | `123456`（首次登录后请立即修改） |
| **New API 用 MySQL** | 数据库（仅内部） | `root` | `NewAPI@stack` |
| **New API 用 MySQL** | 应用连接用 | `newapi` | `123456` |
| **CLIProxyAPI** | 调用 API 时的 Key | 请求头带 `Authorization: Bearer llm-stack-default-key` | 修改 compose 内 `configs.cliproxy_config_yaml.content` 中 api-keys |
| **CLIProxyAPI 管理界面** | 管理后台登录 | 当前地址填 `http://服务器IP:8317` | 管理密钥：`llm-stack-management-key`（在 configs 中 `remote-management.secret-key` 可改） |
| **LobeHub** | 聊天前端 | 无预设管理员 | 首次访问可自注册账号 |
| **RustFS（S3）** | 控制台 / S3 兼容 | `admin` | `rustfs123` |
| **PostgreSQL（LobeHub 用）** | 数据库（仅内部） | `postgres` | `lobechat123` |

以上密码均在 `docker-compose.yml` 中可改；生产环境务必修改默认密码。

## 配置说明

- **New API**：首次访问 3000 端口完成初始化；数据库与 Redis 已内置。
- **CLIProxyAPI**：配置由 compose 内 `configs` 内联注入；管理界面首次启动后约 30 秒内可能 404（拉取面板资源），稍后刷新即可。修改 api-keys 或管理密钥请编辑 `docker-compose.yml` 中 `configs.cliproxy_config_yaml.content`。参见 [官方文档](https://help.router-for.me/docker/docker-compose)。
- **LobeHub**：需设置 `KEY_VAULTS_SECRET`（加密存储用，compose 中已给默认值，生产请用 `openssl rand -base64 32` 生成并替换）；PostgreSQL、Redis、RustFS/S3、SearXNG 已配置，见下方「为什么 Lobe 用这么多组件」。

## 为什么 LobeHub 用这么多组件？

LobeHub 是**带服务端的完整聊天前端**，不是纯静态页：

| 组件 | 作用 |
|------|------|
| **PostgreSQL** | 存用户、会话、消息、配置等结构化数据 |
| **Redis** | 会话/缓存与实时能力 |
| **RustFS（S3）** | 存图片、文件等对象存储，聊天里的上传与多模态依赖它 |
| **SearXNG** | 可选：联网搜索（对话内搜索） |

New API / CLIProxyAPI 只做** API 转发与密钥管理**，无用户界面、不存会话；LobeHub 负责**界面 + 会话持久化 + 文件存储**，所以需要数据库和对象存储。若只想用 API、不需要聊天 UI，可以只跑 New API + CLIProxyAPI，不启动 lobe 相关服务。

## 故障排除

- **LobeHub 报错 `KEY_VAULTS_SECRET` is not set**：compose 中已配置默认值；生产环境请用 `openssl rand -base64 32` 生成并替换 `KEY_VAULTS_SECRET`。
- **CLIProxyAPI 不断重启、日志出现 `config.yaml: is a directory`**：多为曾用 bind 挂载 `./config.cliproxy.yaml` 且宿主机无该文件，Docker 会建为目录。解决：使用本仓库当前 compose（已改为 configs 注入，无需该文件），或删除该目录并放入真正的配置文件后重建容器。
- **管理界面 404**：启动后面板从 GitHub 拉取，约 30 秒内再访问或刷新即可。

## 停止与清理

```bash
docker compose down
# 需同时删除数据卷时：docker compose down -v
```

## 相关链接

- [New API](https://github.com/Calcium-Ion/new-api) · [文档](https://docs.newapi.pro/)
- [CLIProxyAPI](https://github.com/router-for-me/CLIProxyAPI) · [文档](https://help.router-for.me/)
- [LobeHub](https://github.com/lobehub/lobehub) · [自托管文档](https://lobehub.com/docs/self-hosting/platform/docker-compose)
