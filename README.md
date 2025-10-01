# n8n 本地部署指南

> 🚀 **基于官方 Docker 方案，内置 PostgreSQL 数据库，支持内网穿透的一键式部署工具**

欢迎使用 n8n 本地部署工具！本项目基于 [n8n 官方 Docker 部署方案](https://docs.n8n.io/hosting/installation/docker/)，为您提供一个完整的、开箱即用的自动化工作流平台。

## ✨ 核心特性

- 🐳 **官方标准部署** - 采用 n8n 官方推荐的 Docker Compose 部署方式
- 🗄️ **PostgreSQL 数据库** - 内置高性能数据库，存储所有工作流和凭证数据
- 🌐 **内网穿透支持** - 提供多种外网访问方案，无需公网IP
- ⚡ **一键式部署** - 简化配置流程，5分钟快速启动服务

## 📊 三种部署方案快速选择

| 方案 | 适用人群 | 访问方式 | 配置难度 | 稳定性 |
|------|----------|----------|----------|---------|
| **标准版** | 局域网使用 | `http://内网IP:5678` | ⭐ | 🟢 稳定 |
| **Ngrok版** | 临时外网访问 | `https://xxx.ngrok.io` | ⭐⭐ | 🟡 一般 |
| **Cloudflare版** | 长期外网访问 | `https://你的域名` | ⭐⭐⭐ | 🟢 很稳定 |

### 🎯 如何选择？

- **只是自己用** → 选择 **标准版**
- **需要临时分享** → 选择 **Ngrok版**
- **需要长期外网访问** → 选择 **Cloudflare版**

## 🗄️ PostgreSQL 数据库说明

> **所有三种方案都包含完整的 PostgreSQL 数据库，无需额外安装**

您的 n8n 数据将安全存储在 PostgreSQL 数据库中：

- 💼 **工作流数据** - 所有创建的自动化工作流
- 🔐 **凭证信息** - 连接第三方服务的认证信息
- 👤 **用户数据** - 用户账户和权限设置
- 📝 **执行历史** - 工作流执行记录和日志

**数据库优势：**
- ✅ 数据持久化存储，容器重启不丢失
- ✅ 高性能，支持大量工作流
- ✅ 支持完整备份和恢复
- ✅ 企业级数据安全

## 🚀 快速部署指南

> **5分钟完成部署，只需要3个步骤！**

### 前置要求：安装 Docker

请确保您的设备已安装 Docker Desktop：

- **Windows/Mac**: 从 [Docker官网](https://www.docker.com/products/docker-desktop/) 下载安装
- **Linux**: 安装 Docker Engine 和 Docker Compose

### 步骤 1️⃣：选择方案并进入目录

```bash
# 根据您的需求选择一种方案：
cd normal        # 标准版 - 局域网使用
cd ngork         # Ngrok版 - 临时外网访问
cd cloudflare    # Cloudflare版 - 长期外网访问
```

### 步骤 2️⃣：配置数据库密码

> **⚠️ 重要：请进入对应目录后，修改该目录下的 `.env` 文件**

打开对应版本目录下的 `.env` 文件，**必须修改**以下数据库配置：

```bash
# 🔐 PostgreSQL 数据库密码（请设置强密码）
POSTGRES_PASSWORD=your_strong_password_here        # 管理员密码
POSTGRES_NON_ROOT_PASSWORD=your_n8n_password_here  # n8n专用密码
```

> **💡 提示**：所有方案都内置 PostgreSQL 数据库，数据会自动持久化保存

**根据所选方案，在对应的 `.env` 文件中额外配置：**

- **标准版** (`normal/.env`) ✅ 无需额外配置
- **Ngrok版** (`ngork/.env`) 📝 需要配置：
  ```bash
  NGROK_AUTHTOKEN=your_ngrok_auth_token_here    # 从 ngrok.com 获取
  NGROK_DOMAIN=your-domain.ngrok-free.app       # 你的 ngrok 域名
  WEBHOOK_URL=https://your-domain.ngrok-free.app
  ```
- **Cloudflare版** (`cloudflare/.env`) ☁️ 需要配置：
  ```bash
  CLOUDFLARE_TUNNEL_TOKEN=your_cloudflare_tunnel_token_here  # 从 Cloudflare 获取
  WEBHOOK_URL=https://n8n.yourdomain.com                    # 你的域名
  ```

> **📝 配置文件位置说明：**
> - 选择 `normal` 版本 → 修改 `normal/.env` 和 `normal/docker-compose.yml`
> - 选择 `ngork` 版本 → 修改 `ngork/.env` 和 `ngork/docker-compose.yml`
> - 选择 `cloudflare` 版本 → 修改 `cloudflare/.env` 和 `cloudflare/docker-compose.yml`

### 步骤 3️⃣：启动服务

```bash
docker-compose up -d
```

等待 1-2 分钟，服务启动完成！

## 🌐 访问您的 n8n

| 方案 | 访问地址 | 说明 |
|------|----------|------|
| **标准版** | `http://你的IP:5678` | 局域网访问 |
| **Ngrok版** | 查看日志获取地址 | 运行 `docker logs ngrok_tunnel` |
| **Cloudflare版** | `https://你的域名` | 直接访问域名 |

> **🔍 查看IP地址**：Windows用 `ipconfig`，Mac/Linux用 `ifconfig`

## ⚙️ 高级配置（可选）

### 🔧 自定义配置

**修改端口**（如 5678 被占用）：
```bash
# 在 .env 文件中修改
N8N_PORT=8080
```

**访问本地文件**：
```yaml
# 在 docker-compose.yml 中取消注释并修改路径
- /your/local/path:/data
```

**设置时区**：
```bash
# 在 .env 文件中修改
TZ=Asia/Shanghai
```

---

## 🛠️ 日常维护

### 🔄 更新 n8n
```bash
docker-compose pull    # 拉取最新镜像
docker-compose up -d   # 重启服务
```

### 📋 查看日志
```bash
docker logs n8n-app           # n8n 应用日志
docker logs n8n-postgres      # PostgreSQL 数据库日志
docker logs ngrok_tunnel      # Ngrok 日志（仅 Ngrok 版）
docker logs cloudflare_tunnel # Cloudflare 日志（仅 Cloudflare 版）
```

### 💾 数据备份与恢复

**备份 PostgreSQL 数据库**：
```bash
docker exec -t n8n-postgres pg_dump -U postgres n8n > n8n_backup.sql
```

**恢复数据库**：
```bash
cat n8n_backup.sql | docker exec -i n8n-postgres psql -U postgres -d n8n
```

---

## 📁 项目结构

```
self-hosting-n8n/
├── README.md                    # 本说明文档
├── normal/                      # 🏠 标准版 - 局域网使用
│   ├── docker-compose.yml
│   ├── .env
│   └── init-data.sh
├── ngork/                       # 🚀 Ngrok版 - 临时外网访问
│   ├── docker-compose.yml
│   ├── .env
│   └── init-data.sh
└── cloudflare/                  # ☁️ Cloudflare版 - 长期外网访问
    ├── docker-compose.yml
    ├── .env
    └── init-data.sh
```

---

## ❓ 常见问题

<details>
<summary><strong>🔍 与官方部署有什么区别？</strong></summary>

本项目基于官方 Docker Compose 部署，额外增加了内网穿透功能，让没有公网 IP 的用户也能实现外网访问。所有版本都包含完整的 PostgreSQL 数据库。
</details>

<details>
<summary><strong>📁 需要修改哪个配置文件？</strong></summary>

每个版本都有独立的配置文件，请根据选择的版本修改对应目录下的文件：

- **标准版** → 修改 `normal/.env` 文件
- **Ngrok版** → 修改 `ngork/.env` 文件
- **Cloudflare版** → 修改 `cloudflare/.env` 文件

**重要**：进入对应目录后再修改配置文件，不要修改其他目录的文件。
</details>

<details>
<summary><strong>💾 如何备份我的数据？</strong></summary>

主要备份 PostgreSQL 数据库即可保存所有工作流和凭证：
```bash
docker exec -t n8n-postgres pg_dump -U postgres n8n > backup.sql
```
</details>

<details>
<summary><strong>🔄 如何更新版本？</strong></summary>

```bash
docker-compose pull && docker-compose up -d
```
</details>

<details>
<summary><strong>🌐 端口被占用怎么办？</strong></summary>

修改 `.env` 文件中的 `N8N_PORT` 为其他端口，然后重启服务：
```bash
N8N_PORT=8080
docker-compose up -d
```
</details>

<details>
<summary><strong>🔐 忘记密码怎么办？</strong></summary>

```bash
docker-compose down              # 停止服务
docker volume rm n8n_data        # 删除数据卷
docker-compose up -d             # 重新启动
```
然后重新设置管理员账号。
</details>

<details>
<summary><strong>🌐 内网穿透不稳定？</strong></summary>

- Ngrok 适合临时测试
- 长期使用建议选择 Cloudflare 版
- 检查网络连接和配置是否正确
</details>

---

## 🔗 相关链接

- [n8n 官网](https://n8n.io/) | [官方文档](https://docs.n8n.io/) | [Docker 部署指南](https://docs.n8n.io/hosting/installation/docker/)
- [Docker 官网](https://www.docker.com/) | [Ngrok 官网](https://ngrok.com/) | [Cloudflare 官网](https://www.cloudflare.com/)

---

## 🎉 开始使用

部署完成后，您就可以开始使用 n8n 创建强大的自动化工作流了！

如果遇到问题，请检查：
1. ✅ Docker Desktop 是否正常运行
2. ✅ `.env` 文件配置是否正确
3. ✅ 查看容器日志排查错误
4. ✅ 确认选择的部署方式符合需求

祝您使用愉快！如有问题可参考 [n8n 官方文档](https://docs.n8n.io/)