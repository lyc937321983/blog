---
title: CSS动画
date: 2024-07-02 16:00:08
tags: CSS动画
comment: 'valine'
categories: 
- css
---

# CSS动画

## 一、动画分类

### 1. 渐变动画（Transition）

- **定义**：通过`transition`属性实现元素从一个状态到另一个状态的平滑过渡。
- **属性**：包括要过渡的属性（如`width`、`height`、`background-color`等）、过渡时间（如`0.5s`）、运动曲线（如`ease`、`linear`等）以及何时开始（可以设置延迟时间）。

### 2. 变形动画（Transform）

- **定义**：通过`transform`属性对元素进行旋转、缩放、倾斜、移动等2D或3D转换。
- 类型：
  - **旋转**（`rotate`）：包括2D旋转和3D旋转，如`rotate(45deg)`、`rotateX(45deg)`等。
  - **缩放**（`scale`）：如`scale(2, 2)`表示在X轴和Y轴上都放大两倍。
  - **倾斜**（`skew`）：如`skew(30deg, 20deg)`表示元素在X轴倾斜30度，Y轴倾斜20度。
  - **移动**（`translate`）：如`translate(50px, 100px)`表示元素向右移动50px，向下移动100px。

### 3. 自定义动画（Animation）

- **定义**：通过`@keyframes`规则定义动画序列，然后使用`animation`属性将动画应用到元素上。
- **属性**：包括动画名称、动画时长、速度曲线、延迟时间、重复次数、动画方向以及执行完毕时的状态等。
- **示例**：定义一个名为`change`的动画，使元素宽度从211px变化到1111px，持续时间为2秒。

### 4. 逐帧动画（Steps Animation）

- **定义**：通过`animation-timing-function`属性的`steps()`函数实现逐帧动画效果，常用于精灵图动画。
- **特点**：动画的每一帧都是离散的，类似于电影胶片中的每一帧。

## 二、自定义动画效果

### 1.动画逐渐改变元素的样式

只有在指定关键帧之后才能使用。关键帧描述了动画序列中特定点上动画元素的外观。

```css
 div{
  width: 200px;
  height: 200px;
  background-color: blue;
  animation-name: square;
  animation-duration: 8s;
}
@keyframes square{
  from {background-color: blue;}
  to {background-color: black;}
}
```

### 2. 虚浮放大

```html
<!DOCTYPE html>  
<html lang="en">  
<head>  
<meta charset="UTF-8">  
<meta name="viewport" content="width=device-width, initial-scale=1.0">  
<style>  
  .zoom-on-hover {  
    width: 100px; /* 示例宽度 */  
    height: 100px; /* 示例高度 */  
    background-color: skyblue; /* 示例背景色 */  
    transition: transform 0.3s ease; /* 平滑过渡效果 */  
  }  
  
  .zoom-on-hover:hover {  
    transform: scale(1.2); /* 鼠标悬停时放大1.2倍 */  
  }  
</style>  
</head>  
<body>  
  
<div class="zoom-on-hover"></div>  
  
</body>  
</html>
```

### 3. 色相旋转动画

在网站的主要部分和按钮上添加色相旋转动画。

例如，天气预报网站的主要部分将因此而变得令人惊艳。

```css
 button {
  background: linear-gradient(35deg, #8C52FF, #C669FF);
  animation: hue-rotate 3s linear infinite alternate;
}
@keyframes hue-rotate {
  to {
    filter: hue-rotate(85deg);
 }
}
```

