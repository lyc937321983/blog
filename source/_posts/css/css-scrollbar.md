---
title: CSS隐藏滚动条和文字
date: 2024-07-02 14:00:22
tags: 隐藏滚动条
comment: 'valine'
categories: 
- css
---

# CSS隐藏滚动条和文字

### 一、隐藏滚动条

```css
/**
*element：需要隐藏的元素
*/

/** 谷歌 */
.element::-webkit-scrollbar { 
    width: 0 !important
}

.element { 
    -ms-overflow-style: none; /** IE 10+ */
    overflow: -moz-scrollbars-none; /** Firefox */
}

```



### 二、隐藏文字

```css

.hidden {
    white-space: nowrap; /* 不换行 */
    overflow: hidden; /* 超出部分隐藏 */
    text-overflow: ellipsis; /* 添加省略号 */
}
```

### 三、设置滚动条的颜色

1.设置谷歌浏览器

```css
/* 整个滚动条区域 */
::-webkit-scrollbar {
    width: 12px;  /* 垂直滚动条宽度 */
    height: 12px; /* 水平滚动条高度 */
}
 
/* 滚动条轨道 */
::-webkit-scrollbar-track {
    background: #f1f1f1;
    border-radius: 6px;
}
 
/* 滚动条滑块 */
::-webkit-scrollbar-thumb {
    background: #888;
    border-radius: 6px;
}
 
/* 滑块悬停状态 */
::-webkit-scrollbar-thumb:hover {
    background: #555;
}
```

2.通用
```css
/* 通用设置 */
body {
    scrollbar-width: thin;
    scrollbar-color: #888 #f1f1f1;
}
 
/* WebKit浏览器增强设置 */
@media screen and (-webkit-min-device-pixel-ratio:0) {
    ::-webkit-scrollbar {
        width: 12px;
        height: 12px;
    }
    ::-webkit-scrollbar-track {
        background: #f1f1f1;
    }
    ::-webkit-scrollbar-thumb {
        background: #888;
        border-radius: 6px;
    }
}
```
