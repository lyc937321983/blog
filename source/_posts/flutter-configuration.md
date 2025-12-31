---
title: Flutter开发环境搭建
date: 2023-12-08 09:00:50
tags: Flutter
comment: 'valine'
categories: 
- Flutter
---

# Flutter入门教程-开发环境搭建


学习Flutter，首先需要搭建好Flutter的开发环境，下面我将一步步带领大家搭建开发环境并且成功运行flutter项目。
Flutter环境配置主要有这几点：
`系统配置要求`
`Java环境`
`Flutter SDK`
`Android 开发环境`

### 一、系统配置要求

操作系统：Windows 7 SP1 或更高的版本（基于 x86-64 的 64 位操作系统）

磁盘空间：除安装 IDE 和一些工具之外还应有至少 1.64 GB 的空间

Git环境：要让 Flutter在开环境中正常使用，就要有git环境

### 二、Java 环境配置
这里需要安装 Java 环境，因为Flutter是基于Android的，这里就不多细说
Java环境下载地址：Java Downloads | Oracle

这里直接下载64位安装包，解压一直点下一步下一步就搞定了.

怎么检测java是否安装成功呢？

1、快捷键： win+R ，输入cmd，按下回车

2、可以选择输入 java  javac  java -version 三个doc命令进行检查

输入java + 回车，出现结果证明安装成功.


### 三、Flutter SDK
 Flutter SDK下载地址：https://flutter.cn/docs/development/tools/sdk/releases


选择最新版本下载即可，下载成功后将压缩包解压，解压存放路径放在你想放置 Flutter SDK 的路径中（我的是D:\Android\flutter）

注意：请勿将 Flutter 安装在需要高权限的文件夹内，例如 C:\Program Files\

我们可以在控制台输入 flutter 命令看是否安装成功，如果输出如下界面就表示flutter安装成功啦：

将 Flutter 的运行文件路径加入到 PATH 环境变量：

右击【此电脑】选择【属性】==>选择【高级系统设置】==>选择【环境变量】，在【用户变量】一栏中，选择【Path】

双击进入Path条目，点击【新建】将你安装的flutter坐在完整路径作为新变量的值

```sh
  path
  E:\flutter_windows_3.16.3-stable\flutter\bin

  FLUTTER_STORAGE_BASE_URL
  https://storage.flutter-io.cn

  PUB_HOSTED_URL
  https://pub.flutter-io.cn
```

FLUTTER_STORAGE_BASE_URL、PUB_HOSTED_URL 用来配置国内的flutter环境

然后一直点击确定就OK啦


在将 Path 变量更新后，打开一个新的控制台窗口：输入 flutter doctor 命令，如果它提示有任何的平台相关依赖，那么你就需要按照指示完成这些配置。

简单来看，doctor是医生的意思，顾名思义就是对flutter环境进行检查，并将检测结果以报告形式呈现出来，然后根据检查报告依次解决现有环境缺陷问题

这里如果是刚安装flutter，有些检查项带有红色的[×]，我这里有显示[√]和[!]

[×]表示还不能正常运行
[!]表示还存在一些问题
只有全部为[√]，系统环境才是完全安装好，你的检查报告才是没毛病的

有叉的选项的一些常见问题

```txt
  1.cmdline-tools component is missing 没有安装cmdline-tools命令工具
  2.Android license status unknown 缺少android证书
  3.Visual Studio - develop Windows apps 没有安装Visual Studio
```

现在就来看看上述这个警告，根据它的提示，我们只需要执行： 

```sh
  flutter doctor --android-licenses 
```
，执行这条命令后，会有一系列选择，全部选择y就好（我也不知道它是个啥，感兴趣的小伙伴可以自行研究）

然后我们再来执行： 
```sh
flutter doctor 
```
全部为√的选项则你的环境就完全安装好了


### 四、设置Android开发环境
已经正确安装flutter开发环境，但是还需要配置下Android的开发环境，因为Flutter 依赖 Android Studio 的全量安装来为其提供 Android 平台的支持

#### 1.安装 Android Studio
Android Studio下载地址：https://developer.android.google.cn/studio

#### 2.安装Android SDK
Android SDK下载地址：https://www.androiddevtools.cn

进入官网我们首先找到 SDK Tools 选项：

点击下载Android SDK压缩包：

下载成功并解压安装到自定义目录，解压后文件目录如下：

运行 flutter doctor 确保 Flutter 已经定位到了你的 Android Studio 的安装位置。
如果 Flutter 并未定位到，运行 
```sh
  flutter config --android-studio-dir <directory> 
```
设置你的 Android Studio 的安装目录

#### 3.创建虚拟机
首先打开我们的Android Studio开发工具，第一次安装打开界面的左侧选择【Plugin】选项，然后搜索并安装【Flutter】插件，安装【flutter】插件的同时一并安装了【Dart】插件。

安装好插件后，我们创建一个Flutter项目：

这里需要选择你的Flutter SDK安装目录，点击【Next】，输入项目名称：

注意：项目命名规范一般是单词小写，多个单词之间用_连接，如：hello_world

填写完毕后点击【Finish】完成，打开项目进去界面，选择工具栏的【Tools】，选择【SDK Manager】

正确填写Android SDK所在目录：

安装所需工具包（这个可以在后期视情况而定选择下载，这里只是做一个演示）：

然后同样在Tools选项下选择【Device Manager】，点击【Create device】按钮创建虚拟机：

这里自行选择机型，然后点击【Next】：

选择一个系统映像并下载（这个过程可能需要几分钟）：

下载完成后点击【Next】，继续点击【Finish】完成，然后点击启动按钮，等待虚拟机开启即可：

#### 4.运行flutter项目
虚拟机启动后，我们只需要点击编辑器右上角debug就可运行查看flutter项目：

这一过程可能需要等待一会儿：

至此，我们的flutter项目就成功运行啦~~

当然，我们修改main.dart文件内容时，模拟器也是实时更新的：


### 五、总结
至此，我们从搭建项目环境到运行flutter项目整个流程梳理完成，再进行一次总结：

##### 1.首先要本身电脑系统配置达到指定要求
##### 2.Java 环境搭建
##### 3.获取Flutter SDK
##### 4.设置Android Studio开发环境
##### 5.创建虚拟机

### 六、相关配置
##### 1.配置android studio :
	https://blog.51cto.com/u_13446/6527477

##### 2.flutter 环境配置，教程：
	https://www.cnblogs.com/libo-web/p/16060590.html