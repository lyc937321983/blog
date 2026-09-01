---
title: Flutter 状态共享-Provider
date: 2025-12-31 14:35:05
tags: Flutter Provider
comment: 'valine'
categories: 
- Flutter
---

# 状态共享-Provider

## 前言

**状态 (State)** 可以理解为应用的当前情况或配置，它包含了用于可能与之交互的所有信息，**状态管理** 就是对这些状态的变化进行管理，以确保用户界面(UI) 能够适时地反映出这些变化。Flutter中的状态管理一般分为两类：

- **局部状态**：**与单一Widget相关的状态**，通常情况下无需与应用中的其它部分分享，如：一个CheckBox是否勾选就是一个局部状态，可以通过 **StatefulWidget** 和 它的 **State对象** 来管理。
- **全局状态**：**跨多个Widget或整个应用共享的状态**，如：用户登录信息，需要在多个屏幕上进行访问和修改。Flutter社区提供了多种状态管理解决方案，如Provider，Riverpod，Bloc，Redux，GetX等。

> 本质上 Flutter 里的 **状态管理** 就是 **传递状态** 和 **基于setState()** 的 **封装**，**状态管理框架** 解决的是 **如何更优雅地共享状态和调用 setState**。

官方推荐使用 **Provider** 来实现状态共享，底层是对 **InheritedWidget(数据发生改变时，可以自动更新依赖的子孙组件)** 的封装与改进，使其更易于使用，再往下共享状态的同时，可以通过 ChangeNotifier 、 Stream 、Future 配合 Consumer 组合出多样的更新模式。

## 1. 添加依赖

直接执行 **flutter pub add provider**

## 2. 创建Model类继承ChangeNotifier

```dart
import 'package:flutter/foundation.dart';

// 继承 ChangeNotifier
class Counter with ChangeNotifier {
  int _count = 0;

  int get count => _count;

  void increment() {
    _count++;
    notifyListeners();	// 通知观察者更新
  }

  void decrement() {
    _count--;
    notifyListeners();
  }
}
```

## 3. 使用ChangeNotifierProvider实例化Model

```dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';

void main() {
  runApp(
    ChangeNotifierProvider(
      create: (context) => Counter(),
      child: MyApp(),
    ),
  );
}
```

## 4. 访问和监听状态变化

在UI中，可以使用 **Consumer组件** 或 **Provider.of()** 来访问Counter实例：

```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text('Provider Example'),
        ),
        body: Center(
          // 使用Consumer组件来监听Counter的对象变化
          child: Consumer<Counter>(
            builder: (context, counter, child) => Text('Value: ${counter.count}'),
          ),
        ),
        floatingActionButton: FloatingActionButton(
          onPressed: () {
            // 访问实例的increment()方法
            Provider.of<Counter>(context, listen: false).increment();
          },
          child: Icon(Icons.add),
        ),
      ),
    );
  }
}
```

以上是监听单个状态变化的写法，如果想同时监听多个，可以使用 MultiProvider 来实现~

**不用EventBus实现跨组件状态共享** 原因：

> **不好用**！需要显示定义各种事件，不好管理，需要显式注册状态改变回调，而且需要在组件销毁时手动解绑，避免内存泄露问题。

