# n8n 自托管部署指南

欢迎使用 n8n 自托管部署工具！这个项目提供了三种不同的部署方式，以满足不同场景下的需求。

## 三种部署方式对比

| 版本 | 适用场景 | 特点 | 额外要求 |
|------|---------|------|---------|
| **普通版** | 局域网内使用或有公网IP | 简单直接，易于部署 | 无特殊要求 |
| **Ngrok隧道版** | 无公网IP但需临时外网访问 | 快速获得外网访问能力 | 需注册ngrok账号并获取令牌 |
| **Cloudflare隧道版** | 长期稳定的外网访问 | 安全性高，稳定可靠 | 需拥有域名并托管在Cloudflare |

## 如何选择适合您的版本

1. **普通版 (normal)**
   - 如果您只需要在局域网内使用n8n
   - 如果您的服务器有固定公网IP
   - 如果您不需要外网访问功能

2. **Ngrok隧道版 (ngork)**
   - 如果您没有公网IP但需要临时的外网访问
   - 如果您想快速测试n8n的外网访问功能
   - 如果您不想购买域名或配置DNS

3. **Cloudflare隧道版 (cloudflare)**
   - 如果您需要长期稳定的外网访问
   - 如果您已经拥有自己的域名并使用Cloudflare管理
   - 如果您对安全性和稳定性有较高要求

## 部署步骤

无论选择哪个版本，基本部署步骤都是相似的：

1. 进入您选择的版本目录（normal、ngork或cloudflare）
2. 复制`.env.example`为`.env`并根据注释修改相关配置
3. 运行`docker-compose up -d`启动服务
4. 按照各版本README.md中的说明访问和使用n8n

## 版本特殊说明

### Ngrok隧道版
使用前需要在[ngrok官网](https://ngrok.com)注册账号并获取授权令牌(NGROK_AUTHTOKEN)。免费账号可能有使用限制，付费账号可以使用自定义域名功能。

### Cloudflare隧道版
使用前需要：
1. 拥有自己的域名并将DNS托管在Cloudflare
2. 在Cloudflare创建隧道并获取隧道令牌
3. 将令牌替换到docker-compose.yml文件中的YOUR_CLOUDFLARE_TUNNEL_TOKEN位置

## 常见问题

1. **如何备份我的n8n数据？**
   - 每个版本的README.md中都有详细的备份与恢复说明

2. **如何更新n8n版本？**
   - 运行`docker-compose pull`拉取最新镜像
   - 然后运行`docker-compose up -d`重新启动服务

3. **如何查看日志？**
   - 运行`docker logs n8n-app`查看n8n应用日志
   - 运行`docker logs n8n-postgres`查看数据库日志

4. **如何让n8n访问本地文件？**
   - 在docker-compose.yml文件中取消注释并修改本地文件夹挂载路径
   - 详细说明请参考各版本的README.md文件