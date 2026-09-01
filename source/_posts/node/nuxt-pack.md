---
title: Nuxt部署到服务器
date: 2026-01-29 11:09:56
tags: Nuxt部署
comment: 'valine'
categories: 
- node
---

# Nuxt部署到服务器

## 一.打包项目文件：

1.首先使用yarn或者npm，pnpm等包管理工具在项目终端构建项目。

```arduino
yarn run build
```

执行构建命令，并输出一个指定的目录，我这里是.ouput文件

2.压缩打包构建输出目录

```lua
tar -cvf nuxt3-test.tar .output
```

该命令将.output文件压缩为nuxt-test.tar名字的文件

## 二. 上传压缩文件到服务器

```ruby
 scp -i C:\Users\zhouzhou\Desktop\xxx.pem nuxt3-test.tar  username@ip:/root/test
```

将压缩包上传到指定服务器的test文件夹中，我这里的服务器密钥是保存在桌面的一个pem文件，username是服务器用户名称，ip就是服务器ip；

## 三.连接服务器并解压文件

```sh
ssh -i 服务器密钥  username@ip
```

连接到服务器后打开压缩包所在的文件夹,当有显示welcome和登陆时间就说明服务器连接成功了

```sh
 cd nuxt-test //进入有压缩包的文件夹
 ls //查看当前文件夹的文件
 tar -xvf nuxt3-test.tar //解压.output文件
```

解压完成后输入 `ls -a`就可以看到.output文件

## 四.使用pm2管理，运行服务，并配置ecosystem.config.cjs环境文件

PM2 是一个流行的用于Node.js 应用程序的进程管理工具，它可以帮助开发者更轻松地管理和运行 Node.js 应用 ，可以帮助我们管理服务的进程和监视服务。可以通过 `npm install pm2`来进行安装、也可以-g进行全局安装

配置ecosystem.config.cjs文件

```java
// 导出一个配置对象，用于 PM2 管理应用程序
module.exports = {
  // apps 数组用于定义要管理的多个应用程序，这里只定义了一个应用
  apps: [
    {
      // 应用程序的名称，用于在 PM2 管理命令中标识该应用
      name: "nuxt-test",
      // 应用程序的入口脚本文件路径，PM2 将从此处启动应用
      script: ".output/server/index.mjs",
      // 要启动的应用程序实例数量，这里设置为 10 个实例
      instances: "10",
      // 执行模式，cluster 表示集群模式，可利用多核 CPU 提高性能
      exec_mode: "cluster",
      // 之前设置 autorestart 为 false，这里又重新设置为 true，表示当应用程序崩溃或退出时，PM2 会自动重启该应用
      autorestart: true,
      // 是否开启文件监听功能，设置为 true 时，当项目文件发生变化，PM2 会自动重启应用
      watch: true,
      // 配置在文件监听时需要忽略的目录或文件，这里忽略了 logs 目录
      ignore_watch: ["logs"], 
      // 注释掉的配置项，用于设置当应用程序使用的内存超过指定值（这里是 1G）时，PM2 会自动重启应用
      // max_memory_restart: '1G',
      // 环境变量配置，在应用程序运行时会注入这些环境变量
      env: {
        // 应用程序监听的主机地址，0.0.0.0 表示监听所有可用的网络接口
        HOST: "0.0.0.0",
        // Node.js 运行环境，设置为 production 表示生产环境
        NODE_ENV: "production",
        // 应用程序监听的端口号
        PORT: 8089,
      },
      // 日志日期格式，用于在日志中显示每条日志记录的时间，这里的格式为年-月-日 时:分，X 可能表示其他时间相关信息
      log_date_format: "YYYY-MM-DD HH:mm X",
      // 标准输出日志文件的名称，应用程序的标准输出信息会被记录到这个文件中
      out_file: "log.log",
      // 是否合并日志，设置为 true 时，会将不同实例的日志合并到同一个文件中
      merge_log: true,
    },
  ],
};
```

配置好文件后就可以启动服务了

```sh
pm2 start ecosystem.config.cjs
```

运行成功后会出现管理服务的一个表单，显示online则表示服务跑成功了，stopping则失败

| id   | name         | namespace | version | mode    | pid  | uptime | ?    | status   | cpu  | mem  | user | watching |
| ---- | ------------ | --------- | ------- | ------- | ---- | ------ | ---- | -------- | ---- | ---- | ---- | -------- |
| 24   | dxjob-PC-dxc | default   | 0.0.0   | cluster | 0    | 0      | 2917 | stopping | 0%   | 0b   | root | disabled |

一些pm2常用语句：

```shell
pm2 stop all：停止所有由 PM2 管理的应用程序进程。
pm2 stop my-app：停止名称为 `my-app` 的应用程序进程。也可以使用进程 ID 来停止特定进程，如 `pm2 stop 0（假设 `my-app 的 ID 是 0）
pm2 restart all：重启所有由 PM2 管理的应用程序进程。
pm2 restart my-app：重启名称为 `my-app` 的应用程序进程，同样也能用进程 ID 来指定。
pm2 delete all：从 PM2 的管理列表中删除所有应用程序进程。
pm2 delete my-app：从管理列表中删除名称为 `my-app` 的应用程序进程。
pm2 list：显示当前由 PM2 管理的所有进程的详细信息，包括进程 ID、名称、状态、CPU 使用率、内存占用等。
pm2 show my-app ：显示名称为 `my-app` 的应用程序进程的详细信息，如启动时间、运行模式等
pm2 logs ：查看所有由 PM2 管理的应用程序的日志输出。
pm2 logs my-app ：只查看名称为 `my-app` 的应用程序的日志。
pm2 flush ：清空所有日志文件。
pm2 monit ：打开一个交互式界面，实时监控所有进程的 CPU 和内存使用情况
pm2 save ：将当前 PM2 管理的所有进程的状态保存下来，以便在服务器重启后能自动恢复这些进程。
```