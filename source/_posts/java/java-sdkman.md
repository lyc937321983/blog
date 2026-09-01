---
title: Java 版本管理 SDKMAN
date: 2026-05-10 09:38:22
tags: Java
comment: 'valine'
categories: 
- SDKMAN
---

# Java 版本管理 SDKMAN! 安装与使用指南

[SDKMAN!](https://link.juejin.cn/?target=https%3A%2F%2Fsdkman.io%2F) 是一个用于管理多个 SDK 版本的工具，支持 Java、Maven、Kotlin、Scala、Groovy 等多种 JVM 语言和工具。

------

### 一、安装 SDKMAN!

#### 1. 系统要求

- Unix 系统（macOS、Linux）或 Windows（需要 WSL/Git Bash/Cygwin）

- 已安装 `curl` 和 `zip/unzip`

  1. 打开 Git Bash，输入以下命令检查这些工具是否齐全：

     ```sh
     curl -V
     unzip -v
     zip -v
     ```
  
  2. 如果提示  zip: command not found，你需要手动安装 zip。

     最简单的方法是通过 Chocolatey（Windows 的包管理器）安装：
     
     ```sh
     choco install unzip zip
     ```
  
     （如果没有安装 Chocolatey，也可以下载 MinGW 或 Cmder 的完整工具包来补充这些 Unix 工具。）
     
  3. 安装Chocolatey
  
  ```shell
  Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
  
  # 验证安装是否成功
  choco --version
  ```
  
  

#### 2. 安装命令

```bash
curl -s "https://get.sdkman.io" | bash
```

如果装一半中断可以先删除再重新装 `$ rm -rf ~/.sdkman` 。

#### 3. 初始化

安装完成后，打开新终端或执行：

```bash
# zsh
echo 'source "$HOME/.sdkman/bin/sdkman-init.sh"' >> ~/.zshrc
source ~/.zshrc

# bash
echo 'source "$HOME/.sdkman/bin/sdkman-init.sh"' >> ~/.bashrc
source ~/.bashrc
```

#### 4. 验证安装

```bash
sdk version
```

------

### 二、Java 安装与管理

#### 1. 查看可用 JDK 版本

```bash
sdk list java
```

#### 2. 安装 JDK

```bash
# 安装最新稳定版
sdk install java

# 安装指定版本（版本号从 list 中获取）
sdk install java 21.0.2-tem

# 安装指定发行商的版本
sdk install java 17.0.10-zulu
```

#### 3. 切换 JDK 版本

```bash
# 临时切换（仅当前终端生效）
sdk use java 21.0.2-tem

# 永久切换（设为默认版本）
sdk default java 21.0.2-tem
```

#### 4. 查看当前使用的版本

```bash
sdk current java
```

#### 5. 查看已安装的版本

```bash
sdk list java | grep installed
```

#### 6. 卸载 JDK

```bash
sdk uninstall java 21.0.2-tem
```

#### 7. 查看 JAVA_HOME

```bash
echo $JAVA_HOME
# 或
sdk home java current
```

#### 8. 常用 JDK 发行版

| 标识     | 发行商          | 说明                 |
| -------- | --------------- | -------------------- |
| `tem`    | Eclipse Temurin | 社区版，推荐使用     |
| `zulu`   | Azul Zulu       | 免费商业支持         |
| `amzn`   | Amazon Corretto | AWS 优化             |
| `graal`  | GraalVM         | 高性能，支持原生编译 |
| `oracle` | Oracle          | 官方版本             |
| `ms`     | Microsoft       | 微软构建版本         |