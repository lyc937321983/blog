---
title: Docker容器部署Java项目完整流程
date: 2026-09-01 13:44:22
tags: Java
comment: 'valine'
categories: 
- Java
---

# Docker容器部署Java项目完整流程

在服务器Docker容器中部署Java项目，整体流程可概括为：
**本地打包 → 上传服务器 → 编写Dockerfile → 构建镜像 → 运行容器 → 验证访问**。以下是详细的操作步骤和注意事项。

------

### **📦 一、本地打包Java项目**

使用Maven或Gradle将项目打包为可执行的JAR文件：

```sh
# Maven打包（跳过测试）
mvn clean package -DskipTests

# 打包完成后，JAR文件通常在 target/ 目录下
```

------

### **📤 二、上传JAR包到服务器**

通过SCP将打包好的JAR文件上传到服务器指定目录：

```sh
scp target/your-app.jar user@server_ip:/opt/app/
```

------

### **📝 三、编写Dockerfile**

在项目根目录（或服务器上JAR包所在目录）创建 `Dockerfile`：

```dockerfile
# 选择基础镜像（根据项目JDK版本选择）
FROM openjdk:17-jdk-slim

# 设置工作目录
WORKDIR /app

# 拷贝JAR包到容器中
COPY your-app.jar app.jar

# 声明端口（仅作文档说明，不会自动开放）
EXPOSE 8080

# 容器启动时执行的命令
ENTRYPOINT ["java", "-jar", "app.jar"]
```

> 💡 **提示**：如果你的项目使用Java 8，可将基础镜像替换为 `openjdk:8-jdk-alpine`。

------

### **🔨 四、构建Docker镜像**

在Dockerfile所在目录执行：

```sh
docker build -t my-java-app:v1.0 .
```

- `-t`：指定镜像名称和标签
- `.`：表示使用当前目录下的Dockerfile

------

### **🚀 五、运行Docker容器**



```sh
docker run -d \
  --name my-app \
  -p 8080:8080 \
  -v /data/logs:/app/logs \
  -e SPRING_PROFILES_ACTIVE=prod \
  --restart on-failure:3 \
  my-java-app:v1.0
```

**关键参数说明：**

表格

| 参数        | 作用                     | 示例                             |
| :---------- | :----------------------- | :------------------------------- |
| `-d`        | 后台运行容器             | —                                |
| `-p`        | 端口映射（宿主机:容器）  | `-p 8080:8080`                   |
| `-v`        | 数据卷挂载（日志持久化） | `-v /data/logs:/app/logs`        |
| `-e`        | 传递环境变量             | `-e SPRING_PROFILES_ACTIVE=prod` |
| `--restart` | 自动重启策略             | `on-failure:3`                   |
| `--memory`  | 限制容器内存             | `--memory=512m`                  |

------

### **✅ 六、验证部署**

```sh
# 查看容器运行状态
docker ps

# 查看容器日志
docker logs my-app

# 验证服务是否可访问
curl http://localhost:8080/health
```

------

### **⚠️ 七、常见踩坑与排查**

**1. 服务监听地址问题**
如果 `application.yml` 中手动写死了 `server.address: 127.0.0.1`，容器外部将无法访问。应改为：

```yaml
server:
  port: 8080
  address: 0.0.0.0
```

**2. 数据库连接地址问题**
容器内的 `localhost` 指向容器自身，而非宿主机。如果数据库部署在宿主机，需使用宿主机的内网IP；如果使用Docker Compose编排，可通过服务名互相访问：

```yaml
# docker-compose.yml 示例
version: '3.8'
services:
  mysql:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: root123
    ports:
      - "3306:3306"
  app:
    image: my-java-app:v1.0
    ports:
      - "8080:8080"
    depends_on:
      - mysql
```

此时数据库连接URL应写为 `jdbc:mysql://mysql:3306/testdb`。

**3. EXPOSE不等于开放端口**
Dockerfile中的 `EXPOSE` 只是文档声明，实际端口映射必须在 `docker run` 时通过 `-p` 参数指定。

**4. 云服务器安全组**
如果服务器本机可以访问但外网不通，需检查云服务器的安全组规则，确保对应端口已放行。

------

### **🛡️ 八、生产环境建议**

- **内存限制**：对Java容器显式指定 `-Xmx`（建议不超过容器memory limit的75%），避免OOM被kill
- **日志管理**：建议将日志输出到stdout/stderr，由Docker统一收集，避免容器销毁后日志丢失
- **镜像标签**：使用语义化版本（如 `v1.2.3`）或Git SHA，避免使用 `latest`
- **健康检查**：在Dockerfile中添加 `HEALTHCHECK` 指令，配合监控工具及时发现异常
- **回滚策略**：保留最近2个历史镜像，出现问题可快速回滚

