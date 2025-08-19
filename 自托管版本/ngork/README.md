# n8n + PostgreSQL 自托管部署指南 (Ngrok隧道版)

你好！欢迎使用这个 n8n 自托管部署工具。你可以把它想象成一个"万能 DIY 工具箱"，能帮你在任何支持 Docker 的设备上（比如云服务器、家用电脑、NAS 等）搭建起强大的自动化工具 n8n。

这套"工具箱"包含三个核心部件：
1.  **n8n**: 大脑，负责设计和运行各种自动化流程。
2.  **PostgreSQL**: 仓库，一个可靠的数据库，专门存放 n8n 的所有数据。
3.  **Ngrok**: 隧道服务，让没有公网IP的设备也能被外网访问。

## 适用场景

这个Ngrok隧道版本适合以下场景：
- 家庭网络中没有公网IP的设备
- 需要临时让外网访问您的n8n实例
- 不想购买域名或配置DNS的用户

## 特别说明

使用此版本需要注册ngrok账号并获取授权令牌(NGROK_AUTHTOKEN)，可能需要付费才能使用自定义域名功能。

---

## 🚀 两步完成部署，轻松上手

### 第一步：配置“蓝图” (`.env` 文件)

`.env` 文件就是我们这个工具箱的“组装蓝图”，里面记录了一些关键信息。你需要根据自己的情况修改它。

1.  **打开 `.env` 文件。**
2.  **修改密码：** 找到 `POSTGRES_PASSWORD` 和 `POSTGRES_NON_ROOT_PASSWORD`，把等号后面的默认密码改成你自己的，一定要复杂一些！这相当于给你的“仓库”上两把锁。
3.  **检查端口：** `N8N_PORT` 默认是 `5678`。这就像你家的门牌号。如果这个数字没被其他程序占用，就不用改。

### 第二步：启动！

万事俱备！现在，回到你存放 `docker-compose.yml` 文件的这个目录，运行下面的命令，我们的“工具箱”就会开始自动组装了。

```bash
docker-compose up -d
```

看到 `done` 或者没有报错信息，就代表启动成功了！

---

## 🎉 如何访问你的 n8n

在浏览器地址栏输入：`http://你的设备IP地址:5678` (如果你修改了端口，请使用你自己的端口号)。

> **小提示：** 如何查找你的设备 IP 地址？
> *   **Windows**: 打开命令提示符，输入 `ipconfig`。
> *   **Mac**: 打开终端，输入 `ifconfig`。
> *   **Linux / 云服务器**: 打开终端，输入 `ip addr`。

第一次登录 n8n，它会引导你设置一个管理员账号和密码，请一定记好。

---

## 📂 如何让 n8n 读写你电脑上的文件？ (可选)

默认情况下，n8n 产生的文件都存储在 Docker 的“隔离区”里，我们不容易直接访问。如果你希望 n8n 能直接读取或保存文件到你电脑的某个文件夹，可以这样做：

1.  **在你电脑上创建一个文件夹**，比如在 D 盘创建一个叫 `n8n_files` 的文件夹。
2.  **打开 `docker-compose.yml` 文件。**
3.  找到第 48 行左右的 `- /path/to/your/local/files:/data`。
4.  **去掉它前面的 `#` 号**，并把 `/path/to/your/local/files` 换成你刚刚创建的文件夹的**绝对路径**。

    *   **Windows 示例:** `- D:/n8n_files:/data`
    *   **Mac / Linux 示例:** `- /Users/yourname/n8n_files:/data`

5.  保存文件，然后重新启动服务：`docker-compose up -d`。

---

## ⚙️ 日常维护 (进阶)

### 更新版本

想给 n8n 升级到最新版？很简单，只需两步：

```bash
# 1. 拉取最新的“零件”
docker-compose pull

# 2. 重新组装
docker-compose up -d
```

### 查看日志

如果遇到问题，可以查看日志来“诊断”：

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