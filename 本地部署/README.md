# n8n 本地部署指南

欢迎使用 n8n 本地部署工具！这个项目提供了三种不同的部署方式，以满足不同场景下的需求。

## 🎯 选择适合您的版本

### 三种版本对比

| 版本 | 适用场景 | 特点 | 额外要求 |
|------|---------|------|---------|
| **普通版** | 局域网内使用或有公网IP | 简单直接，易于部署 | 无特殊要求 |
| **Ngrok隧道版** | 无公网IP但需临时外网访问 | 快速获得外网访问能力 | 需注册ngrok账号并获取令牌 |
| **Cloudflare隧道版** | 长期稳定的外网访问 | 安全性高，稳定可靠 | 需拥有域名并托管在Cloudflare |

### 如何选择

1. **普通版 (normal)** - 适合：
   - 只需要在局域网内使用n8n
   - 服务器有固定公网IP
   - 不需要外网访问功能

2. **Ngrok隧道版 (ngork)** - 适合：
   - 没有公网IP但需要临时外网访问
   - 快速测试n8n的外网访问功能
   - 不想购买域名或配置DNS

3. **Cloudflare隧道版 (cloudflare)** - 适合：
   - 需要长期稳定的外网访问
   - 已拥有域名并使用Cloudflare管理
   - 对安全性和稳定性有较高要求

## 📋 部署前准备

### 1. 安装Docker Desktop

在开始部署之前，请确保你的设备已安装Docker Desktop：

- **Windows用户**: 访问 [Docker官网](https://www.docker.com/products/docker-desktop/) 下载并安装Docker Desktop for Windows
- **Mac用户**: 访问 [Docker官网](https://www.docker.com/products/docker-desktop/) 下载并安装Docker Desktop for Mac
- **Linux用户**: 参考官方文档安装Docker Engine和Docker Compose

安装完成后，请启动Docker Desktop并确保它正在运行。

### 2. 进入对应目录

根据您选择的版本，进入对应的目录：
```bash
cd normal        # 普通版
# 或
cd ngork         # Ngrok隧道版
# 或
cd cloudflare    # Cloudflare隧道版
```

## ⚙️ 快速开始

**三步完成部署：**

### 第一步：选择版本并进入目录
```bash
cd normal        # 普通版 - 局域网使用
# 或
cd ngork         # Ngrok版 - 临时外网访问
# 或
cd cloudflare    # Cloudflare版 - 长期外网访问
```

### 第二步：配置环境变量

**打开 `.env` 文件，修改以下必须配置：**

**🔒 数据库密码（所有版本必须修改）**
```
POSTGRES_PASSWORD=your_strong_password_here
POSTGRES_NON_ROOT_PASSWORD=your_n8n_password_here
```

**版本特有配置（根据所选版本添加）：**

**普通版** - 无需额外配置

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

## 🎯 访问你的n8n

### 普通版访问
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

# ⚙️ 日常维护 (所有版本通用)

## 更新版本

```bash
# 1. 拉取最新的"零件"
docker-compose pull

# 2. 重新组装
docker-compose up -d
```

## 查看日志

```bash
# 查看 n8n 应用的日志
docker logs n8n-app

# 查看数据库的日志
docker logs n8n-postgres

# 查看额外服务的日志（根据版本）
docker logs ngrok_tunnel          # Ngrok版本
docker logs cloudflare_tunnel     # Cloudflare版本
```

## 备份与恢复

**备份：**
```bash
# 备份数据库
docker exec -t n8n-postgres pg_dump -U postgres n8n > n8n_backup.sql
# 备份 n8n 配置
docker run --rm -v n8n_data:/source -v $(pwd):/backup alpine tar -czf /backup/n8n_data.tar.gz -C /source .
```

**恢复：**
```bash
# 恢复数据库
cat n8n_backup.sql | docker exec -i n8n-postgres psql -U postgres -d n8n
# 恢复 n8n 配置
docker run --rm -v n8n_data:/target -v $(pwd):/backup alpine sh -c "rm -rf /target/* && tar -xzf /backup/n8n_data.tar.gz -C /target"
```

---

## 📁 目录结构

```
本地部署/
├── README.md                    # 本文件
├── normal/                      # 普通版（局域网/公网IP）
│   ├── docker-compose.yml
│   ├── .env
│   └── init-data.sh
├── ngork/                       # Ngrok隧道版（临时外网访问）
│   ├── docker-compose.yml
│   ├── .env
│   └── init-data.sh
└── cloudflare/                  # Cloudflare隧道版（长期稳定外网访问）
    ├── docker-compose.yml
    ├── .env
    └── init-data.sh
```

---

## 常见问题

1. **如何备份我的n8n数据？**
   - 参考上文中的备份与恢复说明

2. **如何更新n8n版本？**
   - 运行`docker-compose pull`拉取最新镜像
   - 然后运行`docker-compose up -d`重新启动服务

3. **如何查看日志？**
   - 运行`docker logs n8n-app`查看n8n应用日志
   - 运行`docker logs n8n-postgres`查看数据库日志
   - 根据版本运行相应的外网服务日志

4. **如何让n8n访问本地文件？**
   - 在docker-compose.yml文件中取消注释并修改本地文件夹挂载路径

5. **端口被占用怎么办？**
   - 修改`.env`文件中的`N8N_PORT`为其他未使用的端口号
   - 重新运行`docker-compose up -d`

6. **忘记密码怎么办？**
   - 停止服务：`docker-compose down`
   - 删除n8n数据卷：`docker volume rm n8n_data`
   - 重新启动：`docker-compose up -d`
   - 重新设置管理员账号

---

## 技术支持

如果您在部署过程中遇到问题，请：

1. 仔细阅读本教程中的每一步
2. 检查Docker Desktop是否正常运行
3. 查看容器日志排查错误
4. 确认`.env`文件配置正确
5. 确认选择的版本符合您的需求

祝您使用愉快！🎉