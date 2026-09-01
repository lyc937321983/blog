---
title: nginx常用命令
date: 2026-09-01 13:50:08
tags: nginx
comment: 'valine'
categories: 
- common
---

# nginx常用命令

以下是 Nginx 的常用命令整理，涵盖日常运维中最核心的操作：

------

### **🚀 服务启停**

表格

| 功能         | 命令                                         | 说明                               |
| :----------- | :------------------------------------------- | :--------------------------------- |
| 启动         | `sudo nginx` 或 `sudo systemctl start nginx` | 启动 Nginx 服务                    |
| 优雅停止     | `sudo nginx -s quit`                         | 等待请求处理完再退出，**生产推荐** |
| 立即停止     | `sudo nginx -s stop`                         | 直接终止，可能中断请求             |
| 重载配置     | `sudo nginx -s reload`                       | **不停机**热更新配置，最常用       |
| 重启服务     | `sudo systemctl restart nginx`               | 有短暂中断，不推荐生产使用         |
| 重新打开日志 | `sudo nginx -s reopen`                       | 日志切割后使用                     |
| 重启服务     | `sudo systemctl reload nginx`                | 通过 systemd 服务管理器触发重载    |

------

### **✅ 配置检查**

表格

| 功能             | 命令                                     |
| :--------------- | :--------------------------------------- |
| 检查配置语法     | `sudo nginx -t`                          |
| 指定配置文件检查 | `sudo nginx -t -c /etc/nginx/nginx.conf` |
| 查看完整生效配置 | `sudo nginx -T`                          |

> 💡 **最佳实践**：修改配置后先执行 `nginx -t` 确认无误，再执行 `nginx -s reload`，可以组合使用：`nginx -t && nginx -s reload`

------

### **🔍 进程与状态查看**

```sh
# 查看 Nginx 进程
ps -ef | grep nginx

# 查看监听端口
ss -lntp | grep nginx

# 查看 Nginx 版本
nginx -v

# 查看详细版本及编译参数（排查模块支持）
nginx -V

# 查看主进程 PID
cat /var/run/nginx.pid
```

------

### **📋 日志排查**

```sh
# 实时查看访问日志
tail -f /var/log/nginx/access.log

# 实时查看错误日志
tail -f /var/log/nginx/error.log

# 查看最近 100 行错误日志
tail -100 /var/log/nginx/error.log
```

------

### **⚙️ 其他实用命令**

表格

| 功能                    | 命令                             |
| :---------------------- | :------------------------------- |
| 指定配置文件启动        | `nginx -c /path/to/nginx.conf`   |
| 前台运行（调试/Docker） | `nginx -g "daemon off;"`         |
| 查找安装路径            | `which nginx` 或 `whereis nginx` |
| 设置开机自启            | `sudo systemctl enable nginx`    |
| 禁用开机自启            | `sudo systemctl disable nginx`   |
| 查看服务状态            | `sudo systemctl status nginx`    |

------

### **⚠️ 进阶：信号控制**

通过 `kill` 向 Nginx 主进程发送信号，可实现更精细的控制：

- `kill -QUIT <pid>` — 优雅关闭
- `kill -HUP <pid>` — 平滑重载配置
- `kill -USR1 <pid>` — 重新打开日志文件
- `kill -USR2 <pid>` — 平滑升级（不中断服务）
- `kill -WINCH <pid>` — 优雅关闭 worker 进程
- `kill -9 <pid>` — 强制杀死（**最后手段，慎用**）

日常运维中，掌握 **启动、停止、重载、检查配置、查看日志** 这几个核心命令就足够应对大部分场景了。