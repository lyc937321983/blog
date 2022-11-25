---
title: javascript手写reduce函数
date: 2022-11-03 10:37:38
tags: reduce
comment: 'valine'
categories: 
- js
---

# javascript手写reduce函数

### 1. 前言

文章会从三个方面应对这道面试题：认识 `reduce`、使用 `reduce`、实现 `reduce`。 阅读本文，你将学到：

```js
1. 认识`reduce`函数；
2. `reduce`函数常见使用场景；
3. 从0到1实现一个`reduce`函数；
```

### 2. 认识 reduce

`reduce()`方法对**数组**中**每个元素**执行一个由您提供的**函数**（升序执行），将其结果汇总为**单个返回值**。

注意几个关键词：**数组、每个元素、函数、单个返回值**。

通俗的解释一下：

```js
1. reduce是数组的一个方法；
2. 会对数组的每个元素进行处理，处理的函数作为形参传入；
3. reduce最后只返回一个结果；
```

语法：

```js
arr.reduce(callback(accumulator, currentValue[, index[, array]])[, initialValue])
// 举例
[1, 2, 3].reduce((acc, cur, index, array) => (arr + cur), initialValue)
```

可以看到，`reduce` 接受两个参数：`callback` 函数、`initialValue` 初始值；其中，传入的处理数组每个元素的函数（`callback`）接受 4 个参数：

- 1. `Accumulator` 累计器
- 1. `Current Value` 当前值
- 1. `Index` 当前索引
- 1. `array` 源数组

每次处理一个元素，`callback` 都有返回值，这个返回值就是累计器，并最终成为单个结果。

### 3. 使用 reduce

常用的使用场景：

#### 3.1 数组里所有值的和

```js
const arr = [1, 2, 3, 4];
const initVal = 0;
const total = arr.reduce((acc, cur, index, array) => {
  console.log(acc, cur);
  // 0 1
  // 1 2
  // 3 3
  // 6 4
  return acc + cur;
}, initVal);
console.log(total); // 输出：10
```

可以看到，在 `reduce` 有初始值的情况下, 第一次迭代累加器 `acc` 的值是 `0`, 当前值 `cur` 是 `1`, 现在把初始值去掉：

```js
const arr = [1, 2, 3, 4];
const total = arr.reduce((acc, cur, index, array) => {
  console.log(acc, cur);
  // 1 2
  // 3 3
  // 6 4
  return acc + cur;
});
console.log(total); // 输出：10
```

可以看到，第一次迭代的累加器 `acc` 的值是 `1`, 当前值 `cur` 是 `2`, 也就是说：**有初始值的情况下: 累计器是从初始值开始累加；没有初始值的情况下：累计器是从数组的第一个元素开始累加**。

#### 3.2 将二维数组转换为一维数组

```js
const res = [1, [2, 3], 4].reduce((acc, cur) => {
  if (typeof cur === "object") {
    acc = acc.concat(cur);
  } else {
    acc.push(cur);
  }
  return acc;
}, []);
console.log(res);
```

主要是通过数组的 `concat` 方法, 累计得到最终的一维数组。当然，二维数组转换为一维数组也可以使用`ECMAScript 2019`提出的`flat`方法(不支持 IE 浏览器)：

```js
[1, [2, 3], 4].flat(1); // 输出：[1, 2, 3, 4]
```

#### 3.3 计算数组中每个元素出现的次数

```js
const arr = ['kobe', 'james', 'kobe', 'jim', 'wade', 'wade', 'kobe'];
const res = arr.reduce((acc, cur) => {
    if(cur in acc) {
        acc[cur]++;
    } else {
        acc[cur] = 1;
    }
    return acc
}, {})
console.log(res) // 输出：{ kobe: 3, james: 1, jim: 1, wade: 2 }
```

#### 3.4 按属性对object进行分类

```js
const players = [
        { name: 'kobe', number: 24 },
        { name: 'James', number: 23 },
        { name: 'Jordon', number: 23 },
        { name: 'George', number: 24 }
];
const groupe = players.reduce((acc, item) => {
        if(!(item.number in acc)) {
            acc[item.number] = [];
        }
        acc[item.number].push(item);
        return acc
}, {})
console.log(groupe)
// 输出
{
    23: [
        { name: 'James', number: 23 },
        { name: 'Jordon', number: 23 },
    ],
    24: [
        { name: 'kobe', number: 24 },
        { name: 'George', number: 24 }
    ]
}
```

#### 3.5 计算数组中的最大值

```js
const arr = [3, 4, 9, 6, 10, 2];
const max = arr.reduce((pre, cur) => Math.max(pre, cur));
console.log(max) // 输出：10

// 也可以使用`Math.max`配合扩展运算符找出最大值
Math.max(...arr)  // 输出：10
```

#### 3.6 数组去重

```js
const arr = [3, 4, 3, 6, 10, 4, 5];
const newArr = arr.reduce((retArr, cur) => {
    if(retArr.indexOf(cur) === -1) {
        retArr.push(cur);
    }
    return retArr
}, [])
console.log(newArr) // 输出：[3, 4, 6, 10, 5]

// 也可以使用Set数据结构来实现
[...new Set(arr)]; // 输出：[3, 4, 6, 10, 5]
```

### 4. 手写实现

通过以上的一些使用场景，我们对reduce函数有了一些了解，先来分析下实现步骤：

- 1. reduce是个函数，且有一个返回值；
- 1. reduce接受两个参数，第一个是callback函数，第二个是初始值（可选）；
- 1. callback函数接受4个参数：累加器、当前值、当前索引、原数组；
- 1. callback函数有返回值，且返回值会赋值给第一个参数；

动手来实现：

```js
Array.prototype.selfReduce = function (callback, initValue) {
        // 获取源数组
        const originArray = this;

        // 判断源数组是否为空，如果为空，抛出异常
        if(!originArray.length) {
            throw new Error('selfReduce of empty array with no initial value');
        }

        // 声明累计器
        let accumulator

        // 是否有初始值情况
        // 设置累计器初始值（如果有初始值，第一次调用`callback`时，`callback`的第一个参数的值为初始值，否则为源数组的第一项）
        if (initValue === undefined) {
            accumulator = originArray[0];
        } else {
            accumulator = initValue;
        }

        // 遍历数组，执行`callback`函数
        for (let i = 0; i < originArray.length; i++) {
            // 如果是最后一次循环，不在执行callback
            if((i + 1) === originArray.length) break;

            // 循环执行 `callback`
            // 这里判断一下`currentValue`
            // 因为有初始值时，`currentValue`是`originArray[i]`
            // 没有初始值时`currentValue`是`originArray[i + 1]`
            accumulator = callback(accumulator, initValue === undefined ? originArray[i + 1] : originArray[i], i, originArray);
        }

        // 把累计器返回出去
        return accumulator
}
```

验证一下：

```js
// 找出最大值
const r = [2, 4, 8, 1].selfReduce((a, b) => Math.max(a, b))
console.log(r) // 输出： 8

// 数组元素求和
const arr = [1, 2, 3, 4];
const initVal = 0;
const total = arr.reduce((acc, cur, index, array) => {
        console.log(acc, cur);
        // 0 1
        // 1 2
        // 3 3
        // 6 4
        return acc + cur;
}, initVal);
console.log(total); // 输出：10
```

至此，就基本实现了`reduce`的功能；

### 5. 总结

面试过程中，经常会被问到一些手写实现原生api的问题；如果真的问到了，也不用特别紧张，其实只要梳理一下思路的话，没有那么难的；如果是函数的话，就要从函数的参数、返回值开始分析，一步一步的去实现，基本是没什么问题的。