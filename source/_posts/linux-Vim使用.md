---
title: Linux 必学：Vim工具使用方法
date: 2025-08-21 09:28:22
tags: Vim
comment: 'valine'
categories: 
- Linux
---

# Linux 必学：Vim工具使用方法          



在 Linux 江湖里，Vim 堪称 "编辑器之神"。它不仅能让你双手不离键盘完成所有操作，更能通过代码高亮、智能补全、多窗口编辑等黑科技，把敲代码变成一场指尖舞蹈。

![图片](https://s3.bmp.ovh/imgs/2025/12/31/e5bf3023e7f4c3e0.png)

## 🚀基础操作：三招解锁 Vim 世界

Vim 有三个核心模式

**命令模式（启动即进入）：**

```
i切换输入模式（Insert）
```

```
x删除光标字符
```

```
:wq保存并退出（经典组合键）
```

**输入模式：尽情打字，左下角会显示**`--INSERT--`

**底线命令模式：**输入`:``set nu`显示行号，`:s/old/new/g`批量替换

## 🛠️快捷键库：让操作飞起来

### 光标移动（告别鼠标！）

- `w 跳到下一个单词开头`
- `gg回到文件顶部`
- `G直达文件末尾`

### 编辑魔法

- `dd删除整行（"Double Delete"）`
- `yy复制当前行（"Yank"）`
- `p粘贴到光标后（"Put"）`

## 🔍进阶技能：效率翻倍秘籍

### 查找替换

- `/keyword向下搜索（回车后n下一个，N上一个）`
- `:%s/old/new/g全文替换（记得备份！）`

### 多窗口操作

- `:sp水平分割窗口`
- `Ctrl+w  + 方向键 切换窗口`

### 宏录制

1. `qa开始录制（a 为任意字母）`
2. 执行操作（如`ddp`交换行）
3. `q 结束录制`
4. `@a重复播放`

## ⚙️个性化配置：打造专属编辑器

编辑`~/.vimrc`文件，添加以下魔法：

```
set nu          "显示行号"
set tabstop=4   "Tab为4空格"
syntax on       "代码高亮"
colorscheme desert "护眼配色"
```

## 💡安装方法

### **Red Hat/CentOS/Fedora 系（DNF/YUM包管理）**

```
# Fedora/CentOS 8+ 使用DNF：
sudo dnf install vim-enhanced
# CentOS 7及以下使用YUM：
sudo yum install vim-enhanced
```

### **Debian/Ubuntu 系（APT包管理）**



```
# 更新软件包列表（可选但推荐）
sudo apt update
# 安装Vim
sudo apt install vim
```

### **openSUSE（Zypper包管理）**

```
sudo zypper install vim
```

### **验证安装**

```
vim --version
# 或运行 vim 进入编辑器
```