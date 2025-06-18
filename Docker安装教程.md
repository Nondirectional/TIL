# Docker 和 Docker Compose 安装教程

## 概述

Docker是一个开源的容器化平台，允许您将应用程序及其依赖项打包到轻量级、可移植的容器中。Docker Compose是用于定义和运行多容器Docker应用程序的工具。

## 系统要求

- 操作系统：Ubuntu 20.04 或更高版本
- 架构：x86_64/amd64
- 内存：至少2GB RAM
- 磁盘空间：至少10GB可用空间

## 安装步骤

### 步骤1：更新系统包索引

```bash
sudo apt update
```

### 步骤2：安装必要的依赖包

```bash
sudo apt install -y apt-transport-https ca-certificates curl gnupg lsb-release
```

**说明**：这些包是Docker安装所需的基础依赖：
- `apt-transport-https`：允许apt通过HTTPS传输
- `ca-certificates`：SSL证书验证
- `curl`：下载工具
- `gnupg`：GPG密钥管理
- `lsb-release`：系统版本信息

### 步骤3：安装Docker和Docker Compose

```bash
sudo apt install -y docker.io docker-compose-v2
```

**说明**：
- `docker.io`：Docker引擎
- `docker-compose-v2`：现代版本的Docker Compose

### 步骤4：启动Docker服务并设置开机自启

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

### 步骤5：将用户添加到docker组

```bash
sudo usermod -aG docker $USER
```

**重要**：执行此命令后，需要注销并重新登录，或重启系统使组权限生效。

### 步骤6：验证安装

#### 检查Docker版本
```bash
docker --version
```

#### 检查Docker Compose版本
```bash
docker compose version
```

#### 运行测试容器（可选）
```bash
# 需要网络连接良好的环境
sudo docker run hello-world
```

## 常用Docker命令

### 容器管理
```bash
# 查看正在运行的容器
docker ps

# 查看所有容器（包括停止的）
docker ps -a

# 启动容器
docker start <container_name>

# 停止容器
docker stop <container_name>

# 删除容器
docker rm <container_name>
```

### 镜像管理
```bash
# 查看本地镜像
docker images

# 删除镜像
docker rmi <image_name>

# 从Docker Hub拉取镜像
docker pull <image_name>
```

### 构建和运行
```bash
# 从Dockerfile构建镜像
docker build -t <image_name> .

# 运行容器
docker run -d --name <container_name> -p <host_port>:<container_port> <image_name>
```

## 常用Docker Compose命令

```bash
# 启动服务
docker compose up

# 后台启动服务
docker compose up -d

# 停止服务
docker compose down

# 查看服务状态
docker compose ps

# 查看服务日志
docker compose logs

# 重新构建服务
docker compose build
```

## Docker Compose文件示例

创建一个`docker-compose.yml`文件示例：

```yaml
version: '3.8'

services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html
    
  database:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: myapp
    volumes:
      - mysql_data:/var/lib/mysql
    ports:
      - "3306:3306"

volumes:
  mysql_data:
```

## 常见问题解决

### 1. 权限问题
如果遇到权限错误，确保：
- 用户已添加到docker组
- 已重新登录或重启系统

### 2. Docker服务未启动
```bash
sudo systemctl status docker
sudo systemctl start docker
```

### 3. 网络连接问题
如果无法拉取镜像，可能需要：
- 检查网络连接
- 配置Docker镜像加速器
- 使用代理设置

### 4. 磁盘空间不足
定期清理不使用的容器和镜像：
```bash
# 清理停止的容器
docker container prune

# 清理未使用的镜像
docker image prune

# 清理所有未使用的资源
docker system prune
```

## 🔧 解决网络连接问题（重要！）

### 问题症状
如果您遇到以下错误：
```
docker: Error response from daemon: Get "https://registry-1.docker.io/v2/": net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers).
```

这表明无法连接到Docker Hub，需要配置镜像加速器。

### 配置国内镜像加速器（推荐方案）

#### 1. 创建Docker配置目录
```bash
sudo mkdir -p /etc/docker
```

#### 2. 配置多个镜像加速器
```bash
echo '{"registry-mirrors":["https://docker.m.daocloud.io","https://dockerproxy.com","https://mirror.baidubce.com","https://reg-mirror.qiniu.com"],"insecure-registries":[],"debug":false,"experimental":false,"log-driver":"json-file","log-opts":{"max-size":"10m","max-file":"3"}}' | sudo tee /etc/docker/daemon.json
```

#### 3. 重启Docker服务
```bash
sudo systemctl daemon-reload
sudo systemctl restart docker
```

#### 4. 验证配置
```bash
sudo docker info | grep -A 10 "Registry Mirrors"
```

#### 5. 测试镜像拉取
```bash
sudo docker pull hello-world
sudo docker run hello-world
```

### 其他镜像加速器选项

#### 阿里云镜像加速器
1. 注册阿里云账号并获取镜像加速器地址
2. 编辑Docker配置文件：

```bash
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://your-accelerator-address.mirror.aliyuncs.com"]
}
EOF
```

#### 网易云镜像加速器
```bash
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://hub-mirror.c.163.com"]
}
EOF
```

## 卸载Docker

如果需要完全卸载Docker：

```bash
# 停止Docker服务
sudo systemctl stop docker

# 卸载Docker包
sudo apt remove -y docker.io docker-compose-v2

# 删除Docker数据（谨慎操作）
sudo rm -rf /var/lib/docker
sudo rm -rf /etc/docker

# 删除docker组
sudo groupdel docker
```


## 参考资源

- [Docker官方文档](https://docs.docker.com/)
- [Docker Compose官方文档](https://docs.docker.com/compose/)
- [Docker Hub](https://hub.docker.com/) 