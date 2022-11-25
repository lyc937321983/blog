---
title: javascript Reflect映射对象
date: 2022-03-03 10:37:38
tags: Reflect
comment: 'valine'
categories: 
- js
---

# JS-Reflect映射对象

### Reflect

`Reflect`是一个对象，翻译过来是反射的意思，它提供了很多操作`JavaScript`对象的方法， 是为了弥补`Object`中对象的一些缺陷。且所有属性和方法都是静态的。

#### 为什么会有Reflect

在早期，`JavaScript`这门语言中的一些内部方法都被部署到了`Object`这个对象上。就例如`getPrototype`、`deinfeProperty`等`API`、类似`in`、`delete`操作符都放到了`Object`对象上了。但`Object`作为一个构造函数（`Reflect`并非一个构造函数，不能通过new关键字调用），这些方法放到它身上并不合适，所以在`ES6`之后的内部新方法会部署到`Reflect`对象中。

#### 使用Reflect对象操作Object对象

Reflect对象让我们操作`Object`对象不再是通过点语法了，而是变成了函数行为。

我们看下面的例子，获取对象属性可以使用`Reflect.get`方法、将对象的属性赋值可以使用`Reflect.set`方法。

```javascript
const obj = {
  name: "_island",
  age: 18
};

// 获取对应属性的值
console.log(obj.name); // _island
console.log(Reflect.get(obj, "name")); // _island

// 对对象的属性赋值操作
obj.name = "abc";
Reflect.set(obj, "name", "abc");
console.log(Reflect.get(obj, "name")); // abc


// 判断一个对象中是否有该属性
console.log("name" in obj); // true
console.log(Reflect.has(obj, "name")); // true
```

#### Reflect中的方法

| 对象中的方法                       | 说明                      |
| ---------------------------------- | ------------------------- |
| Reflect.apply()                    | 对一个函数进行`apply`调用 |
| Reflect.construct()                | 对构造函数进行`new`操作   |
| Reflect.defineProperty()           | 定义一个属性              |
| Reflect.deleteProperty()           | 删除一个属性              |
| Reflect.get()                      | 获取一个属性              |
| Reflect.getOwnPropertyDescriptor() | 获取一个属性描述符        |
| Reflect.getPrototypeOf()           | 获取一个对象的原型        |
| Reflect.has()                      | 判断一个属性是否在对象中  |
| Reflect.isExtensible()             | 判断可以扩展              |
| Reflect.ownKeys()                  | 获取一个对象中的`key`集合 |
| Reflect.preventExtensions()        | 使一个对象不可扩展        |
| Reflect.set()                      | 设置一个属性              |
| Reflect.setPrototypeOf()           | 设置一个对象的原型        |

`Reflect`对象中一些方法与`Object`相同，但它们存在一些细微的区别，如果你想更加了解可以阅读[Reflect和Object中的方法区别](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FJavaScript%2FReference%2FGlobal_Objects%2FReflect%2FComparing_Reflect_and_Object_methods)。

在返回值方便`Reflect`对象中的方法设计的更加合理。比如`defineProperty`方法，如果没有将属性设置成功，在`Reflect`中会返回`boolean`值，而`Object`对象中如果没有定义成功则会抛出`TypeError`。

#### Reflect搭配Proxy

`Reflect`对象中的方法和上一篇文章将到的`Proxy`对象的方法的对应的，`Proxy`对象中的方法也能在`Reflect`对象中调用。

通常我们将`Reflect`对象搭配`Proxy`一起使用，我们看下面这个`Reflect`搭配`Proxy`对象使用的案例。

```javascript
const obj = {
  name: "_island"
};

const objProxy = new Proxy(obj, {
  get: function (target, key, receiver) {
    // 原来的写法
    // return target[key]
    // 使用Reflect
    return Reflect.get(target, key, receiver);
  },
  set: function (target, key, newVal, receiver) {
    // 原来的写法
    // target[key]=newVal
    // 使用Reflect
    Reflect.set(target, key, newVal, receiver);
  },
});

objProxy.name = "abc";
console.log(objProxy.name); // abc
```

在上面，`Proxy`对象中`get`、`set`捕获器多了一个`receiver`参数，这是这两个捕获器特有的，这个`receiver`参数是当前代理的目标。

当`Proxy`和`Reflect`搭配使用时，`Proxy`对象会拦截对应的操作，后者完成对应的操作，如果传入`receiver`，那么`Reflect.get`属性会触发`Proxy.defineProperty`捕获器。我们再上面这里案例上再新增一些代码。

```javascript
const obj = {
  name: "_island"
};

const objProxy = new Proxy(obj, {
  get: function (target, key, receiver) {
    // 原来的写法
    // return target[key]
    // 使用Reflect
    console.log(receiver);
    return Reflect.get(target, key, receiver);
  },
  set: function (target, key, newVal, receiver) {
    // 原来的写法
    // target[key]=newVal
    // 使用Reflect
    Reflect.set(target, key, newVal, receiver);
  },
  defineProperty: function (target, key, attr) {
    console.log("defineProperty");
    console.log(target, key, attr);
    Reflect.defineProperty(target, key, attr);
  }
});

objProxy.name = "abc";
console.log(objProxy.name); 
// defineProperty
// { name: '_island' } name { value: 'abc' }
// { name: 'abc' }
// abc
```

传入在我们获取代理对象中的`name`属性时，当`Reflect`有`receiver`参数传入时，获取属性值时会获取到`receiver`中的，所以会触发`defineProperty`捕获器，如果没有传入`receiver`参数，则不会触发`defineProperty`捕获器。

#### 总结

- `Reflect`对象中集合了`JavaScript`内部方法
- 操作`Object`对象的方式变成了函数行为
- `Reflect`对象中的方法返回结果更加合理