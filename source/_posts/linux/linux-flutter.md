---
title: Flutter 常用命令
date: 2023-12-15 09:13:22
tags: Flutter common
comment: 'valine'
categories: 
- common

---

# Flutter 常用命令

### 一、环境安装

```sh
flutter --version  #查看当前安装的flutter 版本
flutter upgrade    #升级当前的flutter 版本
flutter doctor 	   #检查环境安装是否完成
flutter emulators  #获取模拟器列表（iOS、Android模拟器）
flutter channel    #查看flutter sdk的所有分支
flutter channel stable #切换sdk分支
flutter			   	#获取flutter所有命令
flutter help		#查看命令的帮助信息
flutter analyze		#分析代码
```

### 二、项目编译运行

```sh
flutter clean  #清空build目录
flutter pub get #获取pub插件包
flutter run --设备名称  #运行项目到指定设备
flutter packages get  #获取flutter项目中以来的包，不包括flutter sdk
flutter packages upgrade #更新flutter项目所有依赖包，不包括flutter sdk
```

### 三、打包

```sh
flutter build apk --release --target-platform android-arm64 #生成指定CPU架构的apk
flutter build ios #iOS打包 这一步并不能生成ipa文件，需要使用Xcode 打包
```

### 四、创建项目

```sh
flutter create --project-name hello_flutter --org cn.coderpig --platforms=android,ios --android-language java --ios-language objc hello_flutter 

# 上述参数解析 (更多参数可以键入 flutter create --help 查看更多详细参数 )：

# --project-name → 项目名称，只能由 小写字母、下划线 和 数字 组成，不然会报错：xxx is not a valid Dart package name
# --org → 项目包名
# --platforms → 限定支持的平台，这里限定只支持 android 和 ios
# --android-language → 设定安卓端项目语言，可选值：java, kotlin(默认)
# --ios-language → 设置iOS端项目语言，可选值：objc, swift(默认)
```

