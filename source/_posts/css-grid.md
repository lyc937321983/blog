---
title: CSS grid布局
date: 2022-02-17 10:50:22
tags: grid
comment: 'valine'
categories: 
- css
---

# 前端布局之Grid布局

#### 1.网格布局方向

`display: grid`指定一个容器采用（纵向）网格布局

`display: inline-grid`指定一个容器采用（横向）网格布局

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width">
  <title>JS Bin</title>
  <style>
span {
  font-size: 2em;
}
#container{
  display: grid;
  grid-template-columns: 50px 50px 50px;
  grid-template-rows: 50px 50px 50px;
}
.item {
  font-size: 2em;
  text-align: center;
  border: 1px solid #e5e4e9;
}
.item-1 {background-color: #ef342a;}
.item-2 {background-color: #f68f26;}
.item-3 {background-color: #4ba946;}
.item-4 {background-color: #0376c2;}
.item-5 {background-color: #c077af;}
.item-6 {background-color: #f8d29d;}
.item-7 {background-color: #b5a87f;}
.item-8 {background-color: #d0e4a9;}
.item-9 {background-color: #4dc7ec;}
    </style>
</head>
<body>
  <span>foo</span>
<div id="container">
  <div class="item item-1">1</div>
  <div class="item item-2">2</div>
  <div class="item item-3">3</div>
  <div class="item item-4">4</div>
  <div class="item item-5">5</div>
  <div class="item item-6">6</div>
  <div class="item item-7">7</div>
  <div class="item item-8">8</div>
  <div class="item item-9">9</div>
</div>
<span>bar</span>
</body>
</html>
```

#### 2.网格布局行和列

`grid-template-columns`属性定义每一列的列**宽**

`grid-template-rows`属性定义每一行的行**高**

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width">
  <title>JS Bin</title>
    <style>
#container{
  display: grid;
  grid-template-columns: 100px 100px 100px;
  grid-template-rows: 100px 100px 100px;
}
.item {
  font-size: 4em;
  text-align: center;
  border: 1px solid #e5e4e9;
}
 .item-1 {background-color: #ef342a;}
.item-2 {background-color: #f68f26;}
.item-3 {background-color: #4ba946;}
.item-4 {background-color: #0376c2;}
.item-5 {background-color: #c077af;}
.item-6 {background-color: #f8d29d;}
.item-7 {background-color: #b5a87f;}
.item-8 {background-color: #d0e4a9;}
.item-9 {background-color: #4dc7ec;}
    </style>
</head>
<body>
<div id="container">
  <div class="item item-1">1</div>
  <div class="item item-2">2</div>
  <div class="item item-3">3</div>
  <div class="item item-4">4</div>
  <div class="item item-5">5</div>
  <div class="item item-6">6</div>
  <div class="item item-7">7</div>
  <div class="item item-8">8</div>
  <div class="item item-9">9</div>
</div>
</body>
</html>
```

**（1）repeat()**

**（2）auto-fill 关键字**

**（3）fr 关键字**

**（4）minmax()**

**（5）auto 关键字**

#### 3.网格布局的行间距和列间距

`grid-row-gap`属性设置行与行的间隔（行间距）

`grid-column-gap`属性设置列与列的间隔（列间距）

`grid-gap`属性是`grid-column-gap`和`grid-row-gap`的合并简写形式

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width">
  <title>JS Bin</title>
        <style>
#container{
  display: grid;
  grid-template-columns: 100px 100px 100px;
  grid-template-rows: 100px 100px 100px;
  grid-row-gap: 20px;
  grid-column-gap: 20px;
    /* grid-gap: 20px 20px; 简称 */
}
.item {
  font-size: 4em;
  text-align: center;
  border: 1px solid #e5e4e9;
}
 .item-1 {background-color: #ef342a;}
.item-2 {background-color: #f68f26;}
.item-3 {background-color: #4ba946;}
.item-4 {background-color: #0376c2;}
.item-5 {background-color: #c077af;}
.item-6 {background-color: #f8d29d;}
.item-7 {background-color: #b5a87f;}
.item-8 {background-color: #d0e4a9;}
.item-9 {background-color: #4dc7ec;}
    </style>
</head>
<body>
<div id="container">
  <div class="item item-1">1</div>
  <div class="item item-2">2</div>
  <div class="item item-3">3</div>
  <div class="item item-4">4</div>
  <div class="item item-5">5</div>
  <div class="item item-6">6</div>
  <div class="item item-7">7</div>
  <div class="item item-8">8</div>
  <div class="item item-9">9</div>
</div>
</body>
</html>
```

#### 4.网格布局的区域

`grid-template-areas`属性用于定义区域

#### 5.网格布局的元素顺序

默认的放置顺序是"先行后列"

`grid-auto-flow`属性决定，默认值是`row`，即"先行后列"。也可以将它设成`column`，变成"先列后行"。