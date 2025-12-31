---
title: javascript 闭包
date: 2021-12-28 11:38:22
tags: 闭包
comment: 'valine'
categories: 
- js
---

# JS闭包

### 原理

闭包是指有权访问另一个函数作用域中的变量的函数

### 作用

1.可以在函数的外部访问到函数内部的局部变量。 

2.让这些变量始终保存在内存中，不会随着函数的结束而自动销毁。

### 代码实现

请看下面的代码：

```js
function init() {
    var name = "Mozilla"; // name 是一个被 init 创建的局部变量
    function displayName() { // displayName() 是内部函数，一个闭包
        alert(name); // 使用了父函数中声明的变量
    }
    displayName();
}
init();
```

 `init()` 创建了一个局部变量 `name` 和一个名为 `displayName()` 的函数。
 `displayName()` 是定义在 `init()` 里的内部函数，并且仅在 `init()` 函数体内可用。
 请注意，`displayName()` 没有自己的局部变量。
 然而，因为它可以访问到外部函数的变量，所以 `displayName()` 可以使用父函数 `init()` 中声明的变量 `name` 。

一个有意思的示例 — 一个 `makeAdder` 函数：

```js
function makeAdder(x) {
  return function(y) {
    return x + y;
  };
}

var add5 = makeAdder(5);
var add10 = makeAdder(10);

console.log(add5(2));  // 7
console.log(add10(2)); // 12
```

