---
title: docker常用命令
date: 2026-09-01 13:49:56
tags: docker
comment: 'valine'
categories: 
- common
---

# docker常用命令

以下是 Docker 日常开发中最常用的命令速查，按使用场景分类整理：

### **📌 基础信息**


```sh
docker --version          # 查看版本
docker info               # 查看系统信息（镜像数、容器数等）
docker help               # 查看帮助
```



### **🖼️ 镜像操作**

```sh
docker pull nginx                     # 拉取镜像（默认 latest）
docker pull mysql:8.0                 # 拉取指定版本
docker images                         # 列出本地所有镜像
docker rmi <镜像名或ID>                # 删除镜像
docker build -t myimage:1.0 .         # 从 Dockerfile 构建镜像
docker image prune -a                 # 删除所有未使用的镜像
docker save -o backup.tar nginx       # 导出镜像为 tar 包
docker load -i backup.tar             # 从 tar 包导入镜像
docker tag <源镜像> <目标镜像:标签>     # 给镜像打标签
```



### **📦 容器操作（最核心）**

```sh
# 创建并启动容器
docker run -d -p 80:80 --name mynginx nginx
# -d 后台运行  -p 端口映射  --name 命名  -e 设置环境变量  -v 挂载数据卷

# 交互式启动（进入容器终端）
docker run -it --name myubuntu ubuntu /bin/bash

# 查看容器
docker ps                  # 查看运行中的容器
docker ps -a               # 查看所有容器（含已停止）
docker inspect <容器名>     # 查看容器详细信息（IP、挂载等）
docker stats <容器名>       # 实时查看资源占用（CPU/内存）

# 生命周期管理
docker start <容器名>       # 启动已停止的容器
docker stop <容器名>        # 优雅停止容器
docker kill <容器名>        # 强制终止容器
docker restart <容器名>     # 重启容器
docker rm <容器名>          # 删除已停止的容器
docker rm -f <容器名>       # 强制删除运行中的容器

# 进入容器（推荐方式）
docker exec -it <容器名> /bin/bash

# 查看日志
docker logs -f <容器名>           # 实时跟踪日志
docker logs --tail 100 <容器名>   # 查看最近100行
docker logs --since 1h <容器名>   # 查看最近1小时日志

# 文件复制
docker cp <容器名>:/容器路径 /主机路径    # 容器 → 主机
docker cp /主机路径 <容器名>:/容器路径    # 主机 → 容器
```



### **🌐 网络管理**

```sh
docker network ls                              # 列出所有网络
docker network create mynet                    # 创建自定义网络
docker network inspect mynet                   # 查看网络详情
docker network connect mynet <容器名>           # 将容器加入网络
docker network disconnect mynet <容器名>        # 将容器移出网络
docker network rm mynet                        # 删除网络
```



### **💾 数据卷管理**


```sh
docker volume create mydata                    # 创建数据卷
docker volume ls                               # 列出所有数据卷
docker volume inspect mydata                   # 查看数据卷详情
docker volume rm mydata                        # 删除数据卷
docker volume prune                            # 删除所有未使用的数据卷

# 启动容器时挂载
docker run -d -v mydata:/app/data nginx        # 命名卷挂载
docker run -d -v /主机路径:/容器路径 nginx       # 绑定挂载
```



### **🧹 系统清理**


```sh
docker system df                # 查看 Docker 磁盘占用
docker system prune             # 清理未使用的资源（容器/镜像/网络）
docker system prune -a          # 更彻底清理（含未使用的镜像）
docker container prune          # 仅清理已停止的容器
```



### **🐙 Docker Compose 常用命令**


```sh
docker compose up -d            # 后台启动所有服务
docker compose down             # 停止并移除容器和网络
docker compose ps               # 查看服务状态
docker compose logs -f          # 实时查看日志
docker compose build            # 重新构建镜像
docker compose restart          # 重启所有服务
```


> 💡 **小技巧**：可以设置别名提高效率，在 `~/.bashrc` 或 `~/.zshrc` 中添加：
>
> ```sh
> alias dk='docker'
> alias dkp='docker ps'
> alias dkrm='docker rm -f'
> ```