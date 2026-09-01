---
title: javascript，new一个函数发生了什么及手写实现
date: 2024-07-17 08:35:38
tags: new function
comment: 'valine'
categories: 
- js
---

# new一个函数发生了什么

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

# 手写实现一个new

### 1、首先，我们回顾一下new的用法：

```js
function Car(make, model, year) {
  this.make = make;
  this.model = model;
  this.year = year;
}

const car1 = new Car('Eagle', 'Talon TSi', 1993);

console.log(car1.make);   //输出: "Eagle"
```

### 2、new的过程发生了什么

1. **创建一个空对象**：创建一个空的简单 JavaScript 对象。为方便起见，我们称之为 `newInstance`。
2. **指定原型链**：将 `newInstance` 的 [[Prototype]] 指向构造函数的 `prototype` 属性，否则 `newInstance` 将保持为一个普通对象，其 [[Prototype]] 为 `Object.prototype`。
3. **更改this指向**：使用给定参数执行构造函数，并将 `newInstance` 绑定为 this 的上下文。
4. **返回值**：返回`newInstance`。

### 3、手写一个new

```js
function myNew(Fun, ...args) {
    let obj = {}
    obj.__proto__ = Fun.prototype
    Fun.apply(obj, args)
    return obj
}
function Person(name,age) {
    this.name = name
    this.age = age
}
let apple = myNew(Person, '一颗苹果','18')
console.log(apple.name);   //输出：一颗苹果
console.log(apple.age);    //输出：18
```

这样就模拟出了一个简化版的`new`函数，其中不好理解的就是第三行的`obj.__proto__ = Fun.prototype`和第四行的`Fun.apply(obj, args)`。

- **...args**：将剩下的元素都放进`args`中
- **obj. __ proto __ = Fun.prototype**：将 `obj` 的原型链指向构造函数的 `prototype` 属性
- **Fun.apply(obj, args)**：将构造函数内部的this绑定到`obj`上，并执行构造函数

*通过这一系列操作，我们就可以拿到构造函数中的 `name`和 `age`属性了*。

### 看似完成了，但其实落了很重要的一点（原型链）

“你不觉得少了些什么吗？”

回想一下，我们通过new函数创建一个对象时，除了引用它的属性，我们还会做些什么呢？

```js
let arr = new Array()
arr.push(4)
arr.unshift(3)
console.log(arr);
```

当然是使用它自带的方法，但是上面并没有给出方法上的引用。这就得引入原型链这个知识点了，我们只需要给Person的原型链上增加我们想要的方法就可以了，实现代码如下：

```js
function myNew(Fun, ...args) {
    let obj = {}
    obj.__proto__ = Fun.prototype
    Fun.apply(obj, args)
    return obj
}
function Person(name,age) {
    this.name = name
    this.age = age
}
Person.prototype.who = function () {
    console.log('我是Person的实例对象');
}
Person.prototype.myname = function () {
    console.log(this.name)
}
let p = myNew(Person, '一颗苹果')
p.who();      // 输出：我是Person的实例对象
p.myname();   // 输出：一颗苹果
console.log(p.name);  // 输出：一颗苹果
```