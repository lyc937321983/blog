---
title: CSS flex布局
date: 2021-12-14 11:38:22
tags: flex
comment: 'valine'
categories: 
- css
---

# 前端布局之Flex布局

#### 1.内容水平排列-左对齐

需要在父节点上添加：display:flex;表示使用Flex布局。

flex-direction:row; /* 表示内容直接横排列 */

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        div{
            display:flex;
            width: 80%;
            height: 300px;
            background-color: pink;
             /* 默认的主轴是 x 轴 行 row  那么y轴就是侧轴喽 */
            /* 我们的元素是跟着主轴来排列的 */
            flex-direction:row; /* 表示内容直接横排列 */
        }
        div span{
            width: 150px;
            height: 100px;
            background-color: purple;
        }
    </style>
</head>
<body>
    <div>
        <span>1</span>
        <span>2</span>
        <span>3</span>
    </div>
</body>
</html>
```

#### 2.内容横排列-反转右对齐

flex-direction:row-reverse

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        div{
            display:flex;
            width: 80%;
            height: 300px;
            background-color: pink;
             /* 默认的主轴是 x 轴 行 row  那么y轴就是侧轴喽 */
            /* 我们的元素是跟着主轴来排列的 */
            flex-direction:row-reverse; /* 表示内容直接横排列 */
        }
        div span{
            width: 150px;
            height: 100px;
            background-color: purple;
        }
    </style>
</head>
<body>
    <div>
        <span>1</span>
        <span>2</span>
        <span>3</span>
    </div>
</body>
</html>
```

####  **3.垂直排列**

flex-direction: column;

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        div{
            display:flex;
            width: 80%;
            height: 300px;
            background-color: pink;
             /* 默认的主轴是 x 轴 行 row  那么y轴就是侧轴喽 */
            /* 我们的元素是跟着主轴来排列的 */
            /* flex-direction:row; 表示内容直接横排列 */
            flex-direction: column;

        }
        div span{
            width: 150px;
            height: 100px;
            background-color: purple;
        }
    </style>
</head>
<body>
    <div>
        <span>1</span>
        <span>2</span>
        <span>3</span>
    </div>
</body>
</html>
```

#### **4.居中对齐**

justify-content:center

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
                div {
            display: flex;
            width: 800px;
            height: 300px;
            background-color: pink;
            /* 默认的主轴是 x 轴 row */
            flex-direction: row;
            /* justify-content: 是设置主轴上子元素的排列方式 */
            /* justify-content: flex-start; */
            /* justify-content: flex-end; */
            /* 让我们子元素居中对齐 */
            /* justify-content: center; */
            /* 平分剩余空间 */
            /* justify-content: space-around; */
            /* 先两边贴边， 在分配剩余的空间 */
            justify-content:center
        }

        div span {
            width: 150px;
            height: 100px;
            background-color: purple;
        }
    </style>
</head>
<body>
    <div>
        <span>1</span>
        <span>2</span>
        <span>3</span>
        <span>4</span>
    </div>
</body>
</html>
```

#### **5.垂直均分**

/* 我们现在的主轴是y轴 */

flex-direction: column;

/* justify-content: center; */

justify-content: space-between;

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        div {
            display: flex;
            width: 800px;
            height: 400px;
            background-color: pink;
            /* 我们现在的主轴是y轴 */
            flex-direction: column;
            /* justify-content: center; */
            justify-content: space-between;
        }

        div span {
            width: 150px;
            height: 100px;
            background-color: purple;
        }
    </style>
</head>

<body>
    <div>
        <span>1</span>
        <span>2</span>
        <span>3</span>
    </div>
</body>

</html>
```

