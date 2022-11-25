---
title: javascript作用域及作用域链
date: 2021-12-14 11:20:38
tags: 作用域
comment: 'valine'
categories: 
- js
---
# JS 作用域及作用域链

### 一、作用域

　　在 Javascript 中，作用域分为 **全局作用域** 和 **函数作用域**

#### 　　全局作用域：

　　　　代码在程序的任何地方都能被访问，window 对象的内置属性都拥有全局作用域。

####  　函数作用域：

　　　　在固定的代码片段才能被访问

　　

　　例子：

```js
var a=10,                  //全局作用域
    b=20;

function fn() {
    var a=100,             //fn作用域
        c=300;
    
    function bar() {
		var a=1000,        //bar作用域
            b=4000;
    }
}
```

　　　　作用域有上下级关系，上下级关系的确定就看函数是在哪个作用域下创建的。如上，fn作用域下创建了bar函数，那么“fn作用域”就是“bar作用域”的上级。

　　　　作用域最大的用处就是隔离变量，不同作用域下同名变量不会有冲突。

　　　　变量取值：**到创建 这个变量 的函数的作用域中取值**

 

### **二、作用域链**

　　**一般情况下，变量取值到 创建 这个变量 的函数的作用域中取值。**

　　**但是如果在当前作用域中没有查到值，就会向上级作用域去查，直到查到全局作用域，这么一个查找过程形成的链条就叫做作用域链。**

```js
var x = 10;

function fn(){
    console.log(x);
}

function show(f){
    var x = 20;
    (function(){
       f();    // 10
    })()  
}

show(fn);
```



```js
var a=10;

function fn() {
	var b=20;
    
    function bar() {
		console.log(a + b); //a=10,b=20 输出30
    }
    
    return bar
}

var x=fn(),
    b=200;
x();
```
