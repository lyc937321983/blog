---
title: 谷歌浏览器高级调试技巧
date: 2022-06-10 09:38:22
tags: 谷歌浏览器
comment: 'valine'
categories: 
- browser
---


# 谷歌浏览器高级调试技巧

### 1.一键重新发起请求

```
1. 选中`Network`
2. 点击`Fetch/XHR`
3. 选择要重新发送的请求
4. 右键选择`Replay XHR`
```



### 2.在控制台快速发起请求

```
1. 选中`Network`
2. 点击`Fetch/XHR`
3. 选择`Copy as fetch`
4. 控制台粘贴代码
5. 修改参数，回车搞定
```



### 3. 复制JavaScript变量

```
假如你的代码经过计算会输出一个**复杂的对象**，且需要被复制下来发送给其他人，怎么办？

1. 使用`copy`函数，将`对象`作为入参执行即可
```



### 4.一键展开所有DOM元素

```
按住`opt`键 + click（需要展开的最外层元素）
```



### 5.截取一张全屏的网页

```
1. 准备好需要截屏的内容
2. `cmd + shift + p` 执行`Command`命令
3. 输入`Capture full size screenshot` 按下回车
```



