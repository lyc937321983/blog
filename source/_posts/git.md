---
title: 常用Git命令
date: 2022-01-07 14:38:29
tags: Git
comment: 'valine'
categories: 
- git
---

# 常用Git命令

### 1.新建Gitee仓库

```js
git init 
git add . // .代表所有文件都添加
git commit -m "lyc" // 引号里面是备注信息
git remote add origin https://gitee.com/RyanChaw/cnjy-parent.git # https://gitee.com/RyanChaw/cnjy-parent.git 是刚刚复制的
git pull --rebase origin master
git push -u origin master

```

### 2.Git拉取代码

```js
git clone 'https://gitee.com/a937321983/blog.git' #将远程仓库克隆到本地
```

### 3.提交代码

```js
git add .
git commit -m "第一次提交"
git pull origin 远程分支名  //将远程的拉下来
git push origin master //将远程的提交上去
```

### 4. git分支管理

```js
git branch //查看本地分支
git branch -a //查看远程分支
git checkout 分支名 //切换分支
```

### 5.查看文件的修改历史

```js
git blame 文件名 // 查看该文件的修改历史
git blame -L 100,10 文件名 // 从100行开始，到110行 逐行查看文件的修改历史
```

### 6.清除

```js
git clean -n // 列出打算清除的档案(首先会对工作区的内容进行提示)
git clean -f // 真正的删除
git clean -x -f // 连.gitignore中忽略的档案也删除
git status -sb (sb是 short branch) // 简洁的输出git status中的信息
```

### 7.查看提交信息

```js
git show HEAD // 查看最后一次提交修改的详细信息 也可以用git show 哈希值 查看对应的内容
git show HEAD^ // 查看倒数第二次的提交修改详细信息
git show HEAD^^ 或者git show HEAD~2 //查看前2次变更
git show HEAD 或 git show 哈希值 或者git show tag(标签名) //都可以查看最近一次提交的详细信息
```

### 8.回撤操作

```js
git commit --amend -m "提交信息" // 回撤上一次提交并与本次工作区一起提交
git reset HEAD~2 --hard // 回撤2步
git reset --files // 从仓库回撤到暂存区
git reset HEAD // 回撤暂存区内容到工作目录
git reset HEAD --soft //回撤提交到暂存区
git reset HEAD --hard // 回撤提交 放弃变更 (慎用)
git reset HEAD^  // 回撤仓库最后一次提交
git reset --hard commitid // 回撤到该次提交id的位置 回撤后本地暂存区可能有内容 本地仓库有要同步的内容 此时 丢弃掉暂存区的内容 并且强制将本地的内容推送至远程仓库 执行下面的命令 git push -u -f origin 分支名 这样就可以完全回撤到提交id的位置
git reset --soft commitid // 回撤到该次提交id的位置 并将回撤内容保存在暂存区
git push -f -u origin 分支名 //所有内容都回撤完了 将回撤后的操作强制推送到远程分支
git push origin/分支名 --force //强制将本地回撤后的操作 强制推送到远程分支
```

### 9.查看全部git子命令

```js
git helper -a 
```

