# One-API + CLIProxyAPI + LobeHub 一键部署

一个 Docker Compose 仓库，用于**一次性**部署以下三个服务：

| 服务 | 说明 | 默认端口 |
|------|------|----------|
| [One-API](https://github.com/songquanpeng/one-api) | LLM API 统一管理与转发 | 3000 |
| [CLIProxyAPI](https://github.com/router-for-me/CLIProxyAPI) | OpenAI/Gemini/Claude 等兼容 API 代理 | 8317 |
| [LobeHub](https://github.com/lobehub/lobehub) | 开源 AI 聊天前端（含 S3、PostgreSQL、Redis） | 3210（Lobe）、9000（RustFS） |

由 [coda8](https://github.com/coda8/coda8) 根据 [xx025/xx025#4](https://github.com/xx025/xx025/issues/4) 创建。

## 前置要求

- Docker 与 Docker Compose（或 `docker compose` v2）
- 足够磁盘与内存（建议 4GB+ 内存，10GB+ 磁盘）

## 快速开始

1. **克隆本仓库**

   ```bash
   git clone https://github.com/coda8/one-api-cliproxy-lobehub.git
   cd one-api-cliproxy-lobehub
   ```

2. **配置环境变量**

   ```bash
   cp .env.example .env
   # 编辑 .env，至少修改 One-API 的 SESSION_SECRET、MySQL 密码，以及 LobeHub 的 POSTGRES_PASSWORD、RUSTFS_SECRET_KEY
   ```

3. **配置 CLIProxyAPI（可选）**

   ```bash
   cp config.cliproxy.example.yaml config.cliproxy.yaml
   # 按需修改 api-keys 等，详见 https://help.router-for.me/
   ```
   若不创建 `config.cliproxy.yaml`，CLIProxyAPI 容器会因挂载文件不存在而可能启动异常，请至少保留一份空配置或从示例复制。

4. **启动全部服务**

   ```bash
   docker compose up -d
   ```

5. **访问**

   - One-API: http://localhost:3000  
   - CLIProxyAPI: http://localhost:8317（主端口）  
   - LobeHub: http://localhost:3210  
   - RustFS（S3）: http://localhost:9000  

## 配置说明

- **One-API**：首次访问 3000 端口完成初始化；数据库使用 Compose 内 MySQL，数据目录见 `oneapi_data` volume。
- **CLIProxyAPI**：需在容器内或挂载目录下配置各厂商登录（Gemini/OpenAI/Claude 等），参见 [官方文档](https://help.router-for.me/docker/docker-compose)。
- **LobeHub**：依赖 PostgreSQL、Redis、RustFS（S3）；`bucket.config.json` 已包含，无需修改即可使用默认 S3 桶。更多环境变量见 [LobeHub 自托管文档](https://lobehub.com/docs/self-hosting/platform/docker-compose)。

## 停止与清理

```bash
docker compose down
# 需同时删除数据卷时：
# docker compose down -v
```

## 相关链接

- [One-API](https://github.com/songquanpeng/one-api)
- [CLIProxyAPI](https://github.com/router-for-me/CLIProxyAPI) · [文档](https://help.router-for.me/)
- [LobeHub](https://github.com/lobehub/lobehub) · [自托管文档](https://lobehub.com/docs/self-hosting/platform/docker-compose)
