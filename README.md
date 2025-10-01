# n8n 本地部署指南

欢迎使用 n8n 本地部署工具！本项目基于 [n8n 官方 Docker 部署方案](https://docs.n8n.io/hosting/installation/docker/)，在官方标准部署基础上，增加了内网穿透功能，让您能够轻松实现外网访问能力。

## 🎯 项目特色

- **官方标准部署** - 采用 n8n 官方推荐的 Docker Compose 部署方式
- **PostgreSQL 数据库** - 使用高性能 PostgreSQL 存储工作流和凭证数据
- **内网穿透支持** - 提供多种外网访问方案，无需公网IP
- **一键式部署** - 简化配置流程，快速启动服务

## 🗄️ 数据库说明

本项目使用 **PostgreSQL 数据库**作为 n8n 的后端数据存储系统，用于存储：
- **工作流数据** - 所有创建的自动化工作流
- **凭证信息** - 连接第三方服务的认证信息
- **用户数据** - 用户账户和权限设置
- **执行历史** - 工作流执行记录和日志

PostgreSQL 提供了高性能、可靠性和数据完整性，确保您的工作流和凭证数据安全存储。

## 🚀 部署方案选择

### 三种部署方式对比

| 部署方式 | 适用场景 | 特点 | 额外要求 |
|---------|---------|------|---------|
| **标准版** | 局域网内使用或有公网IP | 基于官方标准部署，简单直接 | 无特殊要求 |
| **Ngrok隧道版** | 无公网IP但需临时外网访问 | 集成 Ngrok 内网穿透，快速获得外网访问 | 需注册ngrok账号并获取令牌 |
| **Cloudflare隧道版** | 长期稳定的外网访问 | 集成 Cloudflare Tunnel，安全稳定 | 需拥有域名并托管在Cloudflare |

### 如何选择部署方式

1. **标准版 (normal)** - 适合：
   - 只需要在局域网内使用n8n
   - 服务器有固定公网IP
   - 不需要外网访问功能
   - 追求最简单的部署方式

2. **Ngrok隧道版 (ngork)** - 适合：
   - 没有公网IP但需要临时外网访问
   - 快速测试n8n的外网访问功能
   - 不想购买域名或配置DNS
   - 需要快速分享工作流给外部用户

3. **Cloudflare隧道版 (cloudflare)** - 适合：
   - 需要长期稳定的外网访问
   - 已拥有域名并使用Cloudflare管理
   - 对安全性和稳定性有较高要求
   - 需要自定义域名访问

## 📋 部署前准备

### 1. 安装Docker Desktop

在开始部署之前，请确保你的设备已安装Docker Desktop：

- **Windows用户**: 访问 [Docker官网](https://www.docker.com/products/docker-desktop/) 下载并安装Docker Desktop for Windows
- **Mac用户**: 访问 [Docker官网](https://www.docker.com/products/docker-desktop/) 下载并安装Docker Desktop for Mac
- **Linux用户**: 参考官方文档安装Docker Engine和Docker Compose

安装完成后，请启动Docker Desktop并确保它正在运行。

### 2. 进入对应目录

根据您选择的部署方式，进入对应的目录：
```bash
cd normal        # 标准版
# 或
cd ngork         # Ngrok隧道版
# 或
cd cloudflare    # Cloudflare隧道版
```

## ⚙️ 快速开始

**三步完成部署：**

### 第一步：选择部署方式并进入目录
```bash
cd normal        # 标准版 - 局域网使用
# 或
cd ngork         # Ngrok版 - 临时外网访问
# 或
cd cloudflare    # Cloudflare版 - 长期外网访问
```

### 第二步：配置环境变量

**打开 `.env` 文件，修改以下必须配置：**

**🔒 PostgreSQL数据库配置（所有版本必须修改）**
```
POSTGRES_PASSWORD=your_strong_password_here        # PostgreSQL管理员密码
POSTGRES_NON_ROOT_PASSWORD=your_n8n_password_here  # n8n专用数据库用户密码
```

**📝 数据库说明：**
- PostgreSQL 将自动创建名为 `n8n` 的数据库
- 所有工作流、凭证和执行数据都存储在此数据库中
- 数据会持久化保存在 Docker 数据卷中，重启容器不会丢失

**部署方式特有配置（根据所选版本添加）：**

**标准版** - 无需额外配置

**Ngrok版** - 需要先到 [ngrok官网](https://ngrok.com) 注册获取令牌
```
NGROK_AUTHTOKEN=your_ngrok_auth_token_here
NGROK_DOMAIN=your-domain.ngrok-free.app
WEBHOOK_URL=https://your-domain.ngrok-free.app
```

**Cloudflare版** - 需要先到 [Cloudflare Dashboard](https://dash.cloudflare.com/) 创建隧道
```
CLOUDFLARE_TUNNEL_TOKEN=your_cloudflare_tunnel_token_here
WEBHOOK_URL=https://n8n.yourdomain.com
```

### 第三步：启动服务
```bash
docker-compose up -d
```

---

## 🌐 访问你的n8n

### 标准版访问
浏览器输入：`http://你的设备IP地址:5678`

> **查找IP地址**：Windows用 `ipconfig`，Mac/Linux用 `ifconfig` 或 `ip addr`

### Ngrok版访问
```bash
docker logs ngrok_tunnel
```
从日志中复制显示的外网地址（如：`https://xxxx.ngrok.io`）

### Cloudflare版访问
浏览器直接访问：`https://n8n.yourdomain.com`

---

## ⚙️ 可选配置

### 端口配置
如果5678端口被占用，在 `.env` 文件中修改：
```
N8N_PORT=8080  # 改为其他未使用的端口
```

### 文件访问配置
如需n8n访问本地文件，在 `docker-compose.yml` 中取消注释第48行：
```yaml
- /path/to/your/files:/data  # 改为你的实际路径
```

### 时区配置
在 `.env` 文件中修改：
```
TZ=Asia/Shanghai  # 根据你所在的时区调整
```

---

# 🛠️ 日常维护 (所有版本通用)

## 更新版本

```bash
# 1. 拉取最新的镜像
docker-compose pull

# 2. 重新启动服务
docker-compose up -d
```

## 查看日志

```bash
# 查看 n8n 应用的日志
docker logs n8n-app

# 查看 PostgreSQL 数据库的日志
docker logs n8n-postgres

# 查看内网穿透服务的日志（根据版本）
docker logs ngrok_tunnel          # Ngrok版本
docker logs cloudflare_tunnel     # Cloudflare版本
```

## 备份与恢复

**备份：**
```bash
# 备份 PostgreSQL 数据库（包含所有工作流和凭证数据）
docker exec -t n8n-postgres pg_dump -U postgres n8n > n8n_backup.sql

# 备份 n8n 应用配置文件
docker run --rm -v n8n_data:/source -v $(pwd):/backup alpine tar -czf /backup/n8n_data.tar.gz -C /source .
```

**恢复：**
```bash
# 恢复 PostgreSQL 数据库（恢复所有工作流和凭证）
cat n8n_backup.sql | docker exec -i n8n-postgres psql -U postgres -d n8n

# 恢复 n8n 应用配置文件
docker run --rm -v n8n_data:/target -v $(pwd):/backup alpine sh -c "rm -rf /target/* && tar -xzf /backup/n8n_data.tar.gz -C /target"
```

---

## 📁 目录结构

```
self-hosting-n8n/
├── README.md                    # 本文件
├── normal/                      # 标准版（基于官方部署）
│   ├── docker-compose.yml
│   ├── .env
│   └── init-data.sh
├── ngork/                       # Ngrok隧道版（官方部署 + Ngrok内网穿透）
│   ├── docker-compose.yml
│   ├── .env
│   └── init-data.sh
└── cloudflare/                  # Cloudflare隧道版（官方部署 + Cloudflare内网穿透）
    ├── docker-compose.yml
    ├── .env
    └── init-data.sh
```

---

## 🔗 相关链接

- [n8n 官方网站](https://n8n.io/)
- [n8n 官方文档](https://docs.n8n.io/)
- [n8n Docker 部署文档](https://docs.n8n.io/hosting/installation/docker/)
- [Docker 官方网站](https://www.docker.com/)
- [Ngrok 官方网站](https://ngrok.com/)
- [Cloudflare 官方网站](https://www.cloudflare.com/)

---

## 常见问题

1. **这个项目与官方部署有什么区别？**
   - 本项目基于官方推荐的 Docker Compose 部署方式，在此基础上增加了内网穿透功能，让没有公网IP的用户也能实现外网访问。

2. **如何备份我的n8n数据？**
   - 参考上文中的备份与恢复说明，主要备份PostgreSQL数据库即可保存所有工作流和凭证。

3. **如何更新n8n版本？**
   - 运行`docker-compose pull`拉取最新镜像
   - 然后运行`docker-compose up -d`重新启动服务

4. **如何查看日志？**
   - 运行`docker logs n8n-app`查看n8n应用日志
   - 运行`docker logs n8n-postgres`查看PostgreSQL数据库日志
   - 根据版本运行相应的内网穿透服务日志

5. **如何让n8n访问本地文件？**
   - 在docker-compose.yml文件中取消注释并修改本地文件夹挂载路径

6. **端口被占用怎么办？**
   - 修改`.env`文件中的`N8N_PORT`为其他未使用的端口号
   - 重新运行`docker-compose up -d`

7. **忘记密码怎么办？**
   - 停止服务：`docker-compose down`
   - 删除n8n数据卷：`docker volume rm n8n_data`
   - 重新启动：`docker-compose up -d`
   - 重新设置管理员账号

8. **内网穿透不稳定怎么办？**
   - Ngrok适合临时测试，如需长期稳定使用建议选择Cloudflare隧道版
   - 检查网络连接和隧道配置是否正确

---

## 技术支持

如果您在部署过程中遇到问题，请：

1. 仔细阅读本教程中的每一步
2. 检查Docker Desktop是否正常运行
3. 查看容器日志排查错误
4. 确认`.env`文件配置正确
5. 确认选择的部署方式符合您的需求
6. 参考 [n8n 官方文档](https://docs.n8n.io/) 获取更多信息

祝您使用愉快！🎉