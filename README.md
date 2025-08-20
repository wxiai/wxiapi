# 🚀 微溪API (WXIAPI)

<p align="center">
  <img src="https://img.shields.io/badge/Version-Latest-brightgreen" alt="Version">
  <img src="https://img.shields.io/badge/License-MIT/Apache--2.0-blue" alt="License">
  <img src="https://img.shields.io/badge/Docker-Ready-blue?logo=docker" alt="Docker">
  <img src="https://img.shields.io/badge/Status-Active-success" alt="Status">
</p>

微溪API(wxiapi) 是基于 NewAPI 二次开发的 AI API 管理系统，遵循 MIT/Apache-2.0 协议发布。

## ✨ 特性

- 🔥 高性能 API 代理
- 🎯 多模型支持
- 📊 详细统计分析
- 🔒 安全认证机制
- 🐳 Docker 一键部署
- 📱 响应式界面

## 🐳 Docker Compose 快速部署

### 前置要求

- Docker 20.10+
- Docker Compose 2.0+

### 🚀 一键安装 Docker

#### Linux 系统（推荐）
```bash
# Docker 官方一键安装脚本
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# 启动 Docker 服务
sudo systemctl start docker
sudo systemctl enable docker

# 将当前用户添加到 docker 组（可选，避免每次使用 sudo）
sudo usermod -aG docker $USER
newgrp docker
```

#### macOS
```bash
# 使用 Homebrew 安装
brew install --cask docker

# 或直接下载：https://www.docker.com/products/docker-desktop
```

#### Windows
下载安装 [Docker Desktop](https://www.docker.com/products/docker-desktop)

### 🔧 Docker Compose 安装

现代 Docker 已内置 Compose 插件，但如需单独安装：

```bash
# 方法一：官方插件安装（推荐）
sudo apt update
sudo apt install docker-compose-plugin

# 方法二：手动安装最新版（v2.39.2）
sudo curl -L "https://github.com/docker/compose/releases/download/v2.39.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# 验证安装
docker compose version
```

> 💡 **注意**：新版本使用 `docker compose`（空格）而不是 `docker-compose`（连字符）

### 🚀 部署步骤

#### 1. 克隆项目
```bash
git clone https://github.com/your-repo/wxiapi.git
cd wxiapi
```

#### 2. 配置环境变量
```bash
# 复制示例配置文件
cp .env.example .env

# 编辑配置文件
vim .env
```

#### 3. 修改重要配置
编辑 `docker-compose.yml` 文件，**必须**修改以下配置：

```yaml
environment:
  # ⚠️ 必须设置强密码
  - SQL_DSN=root:your_strong_password@tcp(mysql:3306)/wxi-api?charset=utf8mb4&parseTime=True&loc=Local
  # ⚠️ 必须设置授权密钥
  - WXI_SECRET=your_secret_key_here
```

同时修改 MySQL 配置：
```yaml
mysql:
  environment:
    MYSQL_ROOT_PASSWORD: your_strong_password  # 与上面密码保持一致
```

#### 4. 启动服务
```bash
# 后台启动所有服务
docker compose up -d

# 查看服务状态
docker compose ps

# 查看服务日志
docker compose logs -f wxi-api
```

#### 5. 验证部署
打开浏览器访问：`http://localhost:3000`

### 🛠️ 常用命令

```bash
# 停止服务
docker compose down

# 重启服务
docker compose restart

# 更新镜像
docker compose pull
docker compose up -d

# 查看日志
docker compose logs -f [service_name]

# 进入容器
docker compose exec wxi-api sh

# 清理数据（⚠️ 危险操作）
docker compose down -v
```

### 📋 服务说明

| 服务 | 端口 | 说明 |
|------|------|------|
| wxi-api | 3000 | 主应用服务 |
| mysql | 3306 | 数据库（内部访问） |
| redis | 6379 | 缓存服务（内部访问） |

### 🔧 配置说明

#### 环境变量说明

| 变量名 | 说明 | 默认值 | 必填 |
|--------|------|--------|------|
| `PORT` | 服务端口 | 3000 | ❌ |
| `SQL_DSN` | 数据库连接字符串 | - | ✅ |
| `REDIS_CONN_STRING` | Redis 连接字符串 | redis://redis | ❌ |
| `WXI_SECRET` | 系统授权密钥 | - | ✅ |
| `GIN_MODE` | 运行模式 | release | ❌ |
| `TZ` | 时区 | Asia/Shanghai | ❌ |

#### 数据持久化

- 数据库数据：`mysql_data` 卷
- 应用数据：`./data` 目录
- 日志文件：`./logs` 目录

### 🚨 安全建议

1. **更改默认密码**：MySQL 和应用的默认密码
2. **设置防火墙**：仅开放必要端口
3. **定期备份**：数据库和配置文件
4. **监控日志**：定期检查应用日志
5. **更新镜像**：定期更新到最新版本

### 📊 监控和维护

#### 健康检查
```bash
# 检查服务健康状态
curl http://localhost:3000/api/status
```

#### 数据备份
```bash
# 备份数据库
docker compose exec mysql mysqldump -uroot -p wxi-api > backup.sql

# 备份应用数据
tar -czf backup-data.tar.gz ./data ./logs
```

### ❓ 常见问题

<details>
<summary><strong>Q: 服务启动失败怎么办？</strong></summary>

1. 检查端口是否被占用：`netstat -tulpn | grep :3000`
2. 查看服务日志：`docker compose logs wxi-api`
3. 确认配置文件格式正确
</details>

<details>
<summary><strong>Q: 如何修改默认端口？</strong></summary>

修改 `docker-compose.yml` 中的端口映射：
```yaml
ports:
  - "8080:3000"  # 将外部端口改为 8080
```
</details>

<details>
<summary><strong>Q: 数据库连接失败？</strong></summary>

1. 确认 MySQL 容器正常运行
2. 检查 `SQL_DSN` 配置是否正确
3. 确认密码与 MySQL 配置一致
</details>

### 🤝 贡献

欢迎提交 Issue 和 Pull Request！

### 📄 许可证

本项目遵循 [MIT](LICENSE) / [Apache-2.0](LICENSE-APACHE) 双许可证。

---

<p align="center">
  Made with ❤️ by WXIAPI Team
</p>
