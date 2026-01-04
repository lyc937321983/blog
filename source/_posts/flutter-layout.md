---
title: Flutter 布局
date: 2025-12-18 17:31:50
tags: Flutter 布局
comment: 'valine'
categories: 
- Flutter
---

# Flutter布局

### 一、row、Column

**`mainAxisAlignment`**:   定义子组件在主轴（水平方向）上的对齐方式

​	MainAxisAlignment.start

​	MainAxisAlignment.end

​	MainAxisAlignment.center

​	MainAxisAlignment.spaceBetween

​	MainAxisAlignment.spaceAround

​	MainAxisAlignment.spaceEvenly

**`mainAxisSize`**:  控制Row在主轴（水平方向）上占用的空间

​	MainAxisSize.max：主轴方向尺寸最大化（默认）

​	MainAxisSize.min：主轴方向尺寸最小化（紧贴子组件总宽度）



**`crossAxisAlignment`**‌：定义子组件在交叉轴（垂直方向）上的对齐方式

​	CrossAxisAlignment.start：

​	CrossAxisAlignment.end：

​	CrossAxisAlignment.center：

​	CrossAxisAlignment.stretch：拉伸子组件以填满交叉轴方向

​	CrossAxisAlignment.baseline：基于文本基线对齐（需子组件有文本）

**`textDirection`**‌：指定主轴方向的文本布局顺序，用于确定`start`和`end`对齐的参考方向

​	TextDirection.ltr： 从左到右（默认）

​	TextDirection.rtl：从右到左（如阿拉伯语场景）

**`verticalDirection`**：决定交叉轴方向的对齐基准（从上到下或从下到上），默认值为 `VerticalDirection.down`（从上到下）。

### padding

1.EdgeInsets

| 构造方法                                                     | 说明                 | 示例                                                |
| :----------------------------------------------------------- | -------------------- | --------------------------------------------------- |
| `EdgeInsets.all(double value)`                               | 四周相等             | `EdgeInsets.all(16)`                                |
| `EdgeInsets.only({left, top, right, bottom})`                | 只设置指定方向       | `EdgeInsets.only(left: 10, top: 5)`                 |
| `EdgeInsets.symmetric({vertical, horizontal})`               | 垂直/水平对称        | `EdgeInsets.symmetric(vertical: 8, horizontal: 12)` |
| `EdgeInsets.fromLTRB(double left, double top, double right, double bottom)` | 按左-上-右-下顺序    | `EdgeInsets.fromLTRB(10, 5, 10, 5)`                 |
| `EdgeInsets.zero`                                            | 所有方向为 0（常量） | `padding: EdgeInsets.zero`                          |