---
title: javascript数组的高级方法
date: 2022-01-07 15:38:22
tags: 高级函数
comment: 'valine'
categories: 
- js
---

# javascript数组的高级方法

#### 一.过滤方法 filter

`filter` 为数组中的每个元素调用一次 `callback` 函数，并利用所有使得 `callback` 返回 true 或[等价于 true 的值](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FGlossary%2FTruthy)的元素创建一个新数组。`callback` 只会在已经赋值的索引上被调用，对于那些已经被删除或者从未被赋值的索引不会被调用。那些没有通过 `callback` 测试的元素会被跳过，不会被包含在新数组中。

> 传入三个参数：
>
> 1. 元素的值（item）
> 2. 元素的索引（index）
> 3. 被遍历的数组本身（arr）

```js
 // filter案例:过滤数组中大于五的数 
 let arr = [5, 6, 8, 10, 24];
 let bigThanEightArr = arr.filter((item, index, arr) => { 
     // console.log(item, index, arr);
     return item > 6 
 }, this);
	console.log(bigThanEightArr); //[8,10,24]
```

#### 二.map方法

`map` 方法创建一个新数组，其结果是该数组中的每个元素是调用一次提供的函数后的返回值。

> 使用三个参数：
> 	1.`currentValue`：callback数组中正在处理的当前元素。
> 	2.`index`可选:`callback` 数组中正在处理的当前元素的索引。
> 	3.`array`可选:`map` 方法调用的数组。
> 	4.`thisArg`可选:执行 `callback` 函数时值被用作`this`。

```js
// map案例:将原数组每个元素都乘以2
let arr = [5, 6, 8, 10, 24];
let doubleArr = arr.map((item, index, arr) => item * 2);
console.log(doubleArr); //[10, 12, 16, 20, 48]
```

#### 三.reduce方法

`reduce`为数组中的每一个元素依次执行`callback`函数，不包括数组中被删除或从未被赋值的元素。

>接受四个参数：
>1.`accumulator`：累计器
>2.`currentValue`：当前值
>3.`currentIndex`：当前索引
>4.`array`：数组

```js
//回调函数第一次执行时，accumulator 和currentValue的取值有两种情况：
//如果调用reduce()时提供了initialValue，accumulator取值为initialValue，currentValue取数组中的第一个值；
//如果没有提供 initialValue，那么accumulator取数组中的第一个值，currentValue取数组中的第二个值。

//无原始参数（initialValue）
[0, 1, 2, 3, 4].reduce(function(accumulator, currentValue, currentIndex, array){
  return accumulator + currentValue;
});
//10

//有原始参数
[0, 1, 2, 3, 4].reduce((accumulator, currentValue, currentIndex, array) => {
    return accumulator + currentValue
}, 10)
//20

```

#### 四.查找数组的元素，find方法

`find()` 方法返回数组中满足提供的测试函数的**第一个元素的值**。否则返回 `undefined`

 >1.`callback`
 >
 >在数组每一项上执行的函数，接收 3 个参数：`element`当前遍历到的元素。`index`可选当   前遍历到的索引。`array`可选数组本身。
 >2.`thisArg`可选
 >执行回调时用作`this` 的对象。

```js
let arr = [10, 5, 6, 80, 12];
let element = arr.find((currentValue, index, arr) => {
	return currentValue > 70
})
console.log(element); //80
```

#### 五.查找数组元素的索引(findIndex)

`indIndex()`方法返回数组中满足提供的测试函数的第一个元素的索引。若没有找到对应元素则返回-1。

>1.`callback`
>
>针对数组中的每个元素, 都会执行该回调函数, 执行时会自动传入下面三个参数:`element`当前元素。`index`当前元素的索引。`array`调用`findIndex`的数组。
>2.`thisArg`可选
>执行回调时用作`this` 的对象。

 

```js
// 以下示例查找数组中素数的元素的索引（如果不存在素数，则返回-1）。
    function isPrime(element, index, array) {
      var start = 2;
      while (start <= Math.sqrt(element)) { //获取平方根
        if (element % start++ < 1) {
          return false;
        }
      }
      return element > 1;
    }
 console.log([4, 6, 8, 12].findIndex(isPrime)); // -1, not found
 console.log([4, 6, 7, 12].findIndex(isPrime)); // 2
```

#### 六.some方法

`some()` 方法测试数组中是不是至少有1个元素通过了被提供的函数测试。它返回的是一个Boolean类型的值。

> `callback`
>
> 用来测试每个元素的函数，接受三个参数：
>
> ​	`element`数组中正在处理的元素。
>
> ​	`index` 可选数组中正在处理的元素的索引值。
>
> ​	`array`可选`some()`被调用的数组。
>
> `thisArg`可选
>
> 执行 `callback` 时使用的 `this` 值。

##### 1.测试数组元素的值

下面的例子检测在数组中是否有元素大于 10。

```js
function isBiggerThan10(element, index, array) {
  return element > 10;
}

[2, 5, 8, 1, 4].some(isBiggerThan10);  // false
[12, 5, 8, 1, 4].some(isBiggerThan10); // true
```

##### 2.使用箭头函数测试数组元素的值

[箭头函数](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FJavaScript%2FReference%2FFunctions%2FArrow_functions) 可以通过更简洁的语法实现相同的用例.

```js
[2, 5, 8, 1, 4].some(x => x > 10);  // false
[12, 5, 8, 1, 4].some(x => x > 10); // true
```

##### 3 判断数组元素中是否存在某个值

此例中为模仿 `includes()` 方法, 若元素在数组中存在, 则回调函数返回值为 `true` :

```js
var fruits = ['apple', 'banana', 'mango', 'guava'];

function checkAvailability(arr, val) {
  return arr.some(function(arrVal) {
    return val === arrVal;
  });
}

checkAvailability(fruits, 'kela');   // false
checkAvailability(fruits, 'banana'); // true
```

##### 4 [使用箭头函数判断数组元素中是否存在某个值

```js
var fruits = ['apple', 'banana', 'mango', 'guava'];

function checkAvailability(arr, val) {
  return arr.some(arrVal => val === arrVal);
}

checkAvailability(fruits, 'kela');   // false
checkAvailability(fruits, 'banana'); // true
```

##### 5 [将任意值转换为布尔类型

```js
var TRUTHY_VALUES = [true, 'true', 1];

function getBoolean(value) {
  'use strict';

  if (typeof value === 'string') {
    value = value.toLowerCase().trim();
  }

  return TRUTHY_VALUES.some(function(t) {
    return t === value;
  });
}

getBoolean(false);   // false
getBoolean('false'); // false
getBoolean(1);       // true
getBoolean('true');  // true
```

#### 七.every方法

`every()` 方法测试一个数组内的所有元素是否都能通过某个指定函数的测试。它返回一个布尔值。

- `callback`

  用来测试每个元素的函数，它可以接收三个参数：`element`用于测试的当前值。`index`可选用于测试的当前值的索引。`array`可选调用 `every` 的当前数组。

- `thisArg`

  执行 `callback` 时使用的 `this` 值。

##### 1检测所有数组元素的大小

  下例检测数组中的所有元素是否都大于 10。

  ```js
function isBigEnough(element, index, array) {
  return element >= 10;
}
[12, 5, 8, 130, 44].every(isBigEnough);   // false
[12, 54, 18, 130, 44].every(isBigEnough); // true
  ```

#####  2 [使用箭头函数]

[箭头函数](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FJavaScript%2FReference%2FFunctions%2FArrow_functions)为上面的检测过程提供了更简短的语法。

```js
[12, 5, 8, 130, 44].every(x => x >= 10); // false
[12, 54, 18, 130, 44].every(x => x >= 10); // true
```

