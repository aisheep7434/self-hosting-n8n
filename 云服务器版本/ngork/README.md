# n8n + PostgreSQL 云服务器部署指南 (Ngrok隧道版)

你好！欢迎使用这个 n8n 云服务器部署工具。这个版本专为云服务器环境优化，并集成了 Ngrok 隧道服务，让你的 n8n 实例能够被外网安全访问。

这套部署方案包含三个核心部件：
1. **n8n**: 大脑，负责设计和运行各种自动化流程。
2. **PostgreSQL**: 仓库，一个可靠的数据库，专门存放 n8n 的所有数据。
3. **Ngrok**: 隧道服务，让没有公网IP的设备也能被外网访问。

## 适用场景

这个Ngrok隧道版本适合以下场景：
- 需要临时让外网访问您的n8n实例
- 不想购买域名或配置DNS的用户
- 希望快速搭建可外网访问的n8n环境

## 特别说明

使用此版本需要注册ngrok账号并获取授权令牌(NGROK_AUTHTOKEN)，可能需要付费才能使用自定义域名功能。

---

## 🚀 两步完成部署，轻松上手

### 第一步：配置"蓝图" (`.env` 文件)

`.env` 文件就是我们这个工具箱的"组装蓝图"，里面记录了一些关键信息。你需要根据自己的情况修改它。

1. **打开 `.env` 文件。**
2. **修改密码：** 找到 `POSTGRES_PASSWORD` 和 `POSTGRES_NON_ROOT_PASSWORD`，把等号后面的默认密码改成你自己的，一定要复杂一些！
3. **配置Ngrok：** 在 [ngrok官网](https://ngrok.com) 注册账号并获取授权令牌，设置 `NGROK_AUTHTOKEN` 和 `NGROK_DOMAIN`。

### 第二步：启动！

万事俱备！现在，回到你存放 `docker-compose.yml` 文件的这个目录，运行下面的命令，我们的"工具箱"就会开始自动组装了。

```bash
docker-compose up -d
```

看到 `done` 或者没有报错信息，就代表启动成功了！

---

## 🎉 如何访问你的 n8n

在浏览器地址栏输入你配置的Ngrok域名，例如：`https://your-domain.ngrok-free.app`

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