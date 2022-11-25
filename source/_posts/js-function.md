---
title: js-call、apply、bind函数
date: 2021-12-27 09:38:22
tags: call、apply、bind
comment: 'valine'
categories: 
- js
---

# js-call、apply、bind函数

函数上下文的三个方法：call()  、 apply()  、 bind() ,他们定义在Function构造函数中，函数执行上下文模，作用可以修改this指向。异同点：都可以修改函数中的this指向，不同点：传参方式不同

### 1.bind()函数实现

bind()语法并不会立即执行函数，而是返回一个修改指向后的新函数，

常用于回调函数

```js
Function.prototype.myBind = function (target) {
    var target = target || window;
    var _args1 = [].slice.call(arguments, 1);
    var self = this;
    var temp = function () {};
    var F = function () {
        var _args2 = [].slice.call(arguments, 0);
        var parasArr = _args1.concat(_args2);
        return self.apply(this instanceof temp ? this : target, parasArr)
    }
    temp.prototype = self.prototype;
    F.prototype = new temp();
    return F;
}
```

### 2.call()函数实现

​	call：函数名.call（this修改后的指向,参数1，参数2....）

​    适用于只有（***一个参数***）的函数

​    应用场景：伪数组排序、检测数据类型

```js
Function.prototype.myCall = function () {
    var ctx = arguments[0] || window;
    ctx.fn = this;
    var args = [];
    for (var i = 1; i < arguments.length; i++) {
        args.push(arguments[i])
    }
    var result = ctx.fn(...args);
    delete ctx.fn;
    return result;
}
```

### 3.apply()函数实现

apply() ：函数名：apply(this修改后的指向,伪数组/数组)

适用于（***多个传参***）的函数

应用场景：伪数转真数组、求数组最大值

```js
Function.prototype.myApply = function () {
    var ctx = arguments[0] || window;
    ctx.fn = this;
    if (!arguments[1]) {
        var result = ctx.fn();
        delete ctx.fn;
        return result;
    }
    var result = ctx.fn(...arguments[1]);
    delete ctx.fn;
    return result;
}
```

