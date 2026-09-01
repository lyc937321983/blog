---
title: 前端常用npm、yarn、pnpm命令
date: 2023-12-15 09:13:22
tags: npm、yarn、pnpm
comment: 'valine'
categories: 
- common

---

# 前端常用命令

## 一、npm命令

### 1.基础命令

```sh
npm init						#初始化一个新的npm项目，跳过npm init命令行接口(CLI)。
npm install						#根据项目中的package.json文件自动下载项目所需的全部依赖。
npm install 包名 --save-dev		#安装的包只用于开发环境，不用于生产环境，会出现在package.json文件中的dependencies属性中。
npm install 包名 --save			#安装的包需要发布到生产环境的，会出现在package.json文件中的dependencies属性中。
npm list						#查看当前目录下已安装的node包。
npm list -g						#查看全局已经安装过的node包。
npm update						 #包名：更新指定包。
npm uninstall 					#包名：卸载指定包。
npm config list					#查看配置信息。
npm info 包名						#查看包的详细信息。
npm search 字符串/正则表达式		#搜索npm仓库。
npm logout						#退出npm的登录状态。
npm login						#登录npm，输入用户名和密码。
npm whoami						#查看当前登录的用户名。
npm cache clean					#清理npm缓存。
npm cache verify				#检查npm缓存的有效性。
```

### 2.进阶命令

```sh
npm config list：列出npm的所有配置。
npm config edit：编辑npm的配置文件。
npm unpublish 包名：从npm仓库中删除指定的包。
npm config get always-auth：获取npm始终进行身份验证的设置。
npm config set always-auth false：取消设置npm始终进行身份验证。
npm config set email [email]**：设置npm的邮箱地址。
npm config set username [username]**：设置npm的用户名。
npm config rm username：删除npm的用户名配置。
npm config rm email：删除npm的邮箱地址配置。
npm config env：打印出配置相关的环境变量。
npm config list：列出所有的配置选项及其值。
npm config delete **：删除特定的配置选项。

# 设置淘宝的镜像源
npm config set registry https://registry.npmmirror.com
# 腾讯云镜像源
npm config set registry http://mirrors.cloud.tencent.com/npm/
# 华为云镜像源
npm config set registry https://mirrors.huaweicloud.com/repository/npm/
# 官方默认全局镜像
npm config set registry https://registry.npmjs.org
# 检查当前镜像
npm config get registry
```

## 二、yarn命令

```sh
#1、安装yarn 
npm install -g yarn

#2、安装成功后，查看版本号： 
yarn --version

#3、初始化项目 
yarn init # 同npm init，执行输入信息后，会生成package.json文件
yarn的配置项： 
yarn config list # 显示所有配置项
yarn config get <key> # 显示某配置项
yarn config delete <key> # 删除某配置项
yarn config set <key> <value> [-g|--global] #设置配置项
yarn config set registry https://registry.npmmirror.com # 添加淘宝源

#4、安装包： 
yarn install # 安装package.json里所有包，并将包及它的所有依赖项保存进yarn.lock
yarn install --flat # 安装一个包的单一版本
yarn install --force # 强制重新下载所有包
yarn install --production # 只安装dependencies里的包
yarn install --no-lockfile # 不读取或生成yarn.lock
yarn install --pure-lockfile # 不生成yarn.lock

#5、添加包（会更新package.json和yarn.lock）
yarn add [package] #  在当前的项目中添加一个依赖包，会自动更新到package.json和yarn.lock文件中
yarn add [package]@[version] #  安装指定版本，这里指的是主要版本，如果需要精确到小版本，使用-E参数
yarn add [package]@[tag] #  安装某个tag（比如beta,next或者latest）

# 不指定依赖类型默认安装到dependencies里，你也可以指定依赖类型：

yarn add --dev/-D #  加到 devDependencies
yarn add --peer/-P #  加到 peerDependencies
yarn add --optional/-O #  加到 optionalDependencies

# 默认安装包的主要版本里的最新版本，下面两个命令可以指定版本：

# 安装包的精确版本。例如yarn add test@1.2.3会接受1.9.1版，但是yarn add test@1.2.3 --exact只会接受1.2.3版

yarn add --exact/-E 

#  安装包的次要版本里的最新版。例如yarn add foo@1.2.3 --tilde会接受1.2.9，但不接受1.3.0

yarn add --tilde/-T 

#6、发布包
yarn publish

#7、移除一个包 
yarn remove <packageName>：移除一个包，会自动更新package.json和yarn.lock

#8、更新一个依赖 
yarn upgrade 用于更新包到基于规范范围的最新版本

#9、运行脚本 
yarn run 用来执行在 package.json 中 scripts 属性下定义的脚本

#10、显示某个包的信息 
yarn info <packageName> 可以用来查看某个模块的最新版本信息

#11、缓存 
yarn cache 
yarn cache list # 列出已缓存的每个包 
yarn cache dir # 返回 全局缓存位置 
yarn cache clean # 清除缓存
```

## 三、pnpm

```sh
# 安装指定版本pnpm
npm install -g pnpm@6.32.2

#安装软件包及其依赖的任何软件包 如果workspace有配置会优先从workspace安装
pnpm add <pkg>
#安装项目所有依赖
pnpm install
#更新软件包的最新版本
pnpm update
#移除项目依赖
pnpm remove
#运行脚本
pnpm run
#创建一个 package.json 文件
pnpm init
#以一个树形结构输出所有的已安装package的版本及其依赖
pnpm list
```

