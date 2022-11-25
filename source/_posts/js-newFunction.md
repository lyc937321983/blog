---
title: javascript，new一个函数发生了什么及手写实现
date: 2022-08-08 14:37:38
tags: new function
comment: 'valine'
categories: 
- js
---

# new一个函数发生了什么及手写实现

new运算符创建一个用户定义的对象类型的实例或具有构造函数的内置对象的实例。

```js
function Foo(name){
    this.name = name;
}
let foo = new Foo('小明');
```

> 当代码执行new Foo时，会发生以下事情：
>
> 1. 一个继承自 Foo.prototype 的新对象被创建
> 2. 使用指定的参数调用构造函数 Foo,并将this绑定到新创建的对象
> 3. 由构造函数返回的对象就是new表达式的结果。如果构造函数没有显式返回一个对象，则使用步骤1创建的对象。(一般情况下，构造函数不返回值，但是用户可以选择主动返回对象，来覆盖正常的对象创建步骤)

了解了new的原理，我们来自己动手实现一个：

```js
// 接收不定参，第一个参数是构造函数，后续参数被构造函数使用
function myNew(fn,...args){
  // 创建一个空对象
  const obj = {};
  // 将该对象的 __proto__ 属性链接到构造函数原型对象
  obj.__proto__ = fn.prototype;
  // 将该对象作为 this 上下文调用构造函数并接收返回值
  const res = fn.apply(obj,args);
  // 如果返回值存在并且是引用数据类型，返回构造函数返回值，否则返回创建的对象
  return typeof res === 'object'?res:obj
}
```