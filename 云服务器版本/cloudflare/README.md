# n8n + PostgreSQL 云服务器部署指南 (Cloudflare隧道版)

你好！欢迎使用这个 n8n 云服务器部署工具。这个版本专为云服务器环境优化，并集成了 Cloudflare 隧道服务，提供更稳定、安全的外网访问方式。

这套部署方案包含三个核心部件：
1. **n8n**: 大脑，负责设计和运行各种自动化流程。
2. **PostgreSQL**: 仓库，一个可靠的数据库，专门存放 n8n 的所有数据。
3. **Cloudflare隧道**: 安全隧道服务，提供稳定、安全的外网访问方式。

## 适用场景

这个Cloudflare隧道版本适合以下场景：
- 需要长期稳定的外网访问
- 已拥有自己的域名并使用Cloudflare管理
- 对安全性和稳定性有较高要求的用户

## 特别说明

使用此版本需要拥有自己的域名，并将域名托管在Cloudflare，同时需要在Cloudflare创建隧道并获取隧道令牌。

---

## 🚀 两步完成部署，轻松上手

### 第一步：配置"蓝图" (`.env` 文件)

`.env` 文件就是我们这个工具箱的"组装蓝图"，里面记录了一些关键信息。你需要根据自己的情况修改它。

1. **打开 `.env` 文件。**
2. **修改密码：** 找到 `POSTGRES_PASSWORD` 和 `POSTGRES_NON_ROOT_PASSWORD`，把等号后面的默认密码改成你自己的，一定要复杂一些！
3. **配置Cloudflare：** 确保你的域名已经托管在Cloudflare，在Cloudflare创建隧道并获取隧道令牌，设置 `WEBHOOK_URL`，在docker-compose.yml文件中将YOUR_CLOUDFLARE_TUNNEL_TOKEN替换为你的隧道令牌。

### 第二步：启动！

万事俱备！现在，回到你存放 `docker-compose.yml` 文件的这个目录，运行下面的命令，我们的"工具箱"就会开始自动组装了。

```bash
docker-compose up -d
```

看到 `done` 或者没有报错信息，就代表启动成功了！

---

## 🎉 如何访问你的 n8n

在浏览器地址栏输入你配置的Cloudflare域名，例如：`https://your.domain.com`

第一次登录 n8n，它会引导你设置一个管理员账号和密码，请一定记好。

---

## ⚙️ 日常维护 (进阶)

### 更新版本

想给 n8n 升级到最新版？很简单，只需两步：

```bash
# 1. 拉取最新的"零件"
docker-compose pull

# 2. 重新组装
docker-compose up -d
```

### 查看日志

如果遇到问题，可以查看日志来"诊断"：

```bash
# 查看 n8n 应用的日志
docker logs n8n-app

# 查看数据库的日志
docker logs n8n-postgres
```

### 备份与恢复

这是一个非常高级的操作，建议在操作前先搜索相关教程。

#### 备份：
```bash
# 备份数据库
docker exec -t n8n-postgres pg_dump -U postgres n8n > n8n_backup.sql
# 备份 n8n 配置
docker run --rm -v n8n_data:/source -v $(pwd):/backup alpine tar -czf /backup/n8n_data.tar.gz -C /source .
```

#### 恢复：
```bash
# 恢复数据库
cat n8n_backup.sql | docker exec -i n8n-postgres psql -U postgres -d n8n
# 恢复 n8n 配置
docker run --rm -v n8n_data:/target -v $(pwd):/backup alpine sh -c "rm -rf /target/* && tar -xzf /backup/n8n_data.tar.gz -C /target"