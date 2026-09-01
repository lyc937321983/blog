---
title: JS新特性（ES10、ES11、ES12）
date: 2023-12-15 10:38:22
tags: ES
comment: 'valine'
categories: 
- js
---

# ES10

ES2019（ES10）新增了如下新特性👇：

- `Array.prototype.{flat, flatMap}`扁平化嵌套数组
- `Object.fromEntries`
- `String.prototype.{trimStart, trimEnd}`
- `Symbol.prototype.description`
- `Optional catch binding`
- Array.prototype.sort() is now required to be stable

## 一、`Array.prototype.{flat, flatMap}` 扁平化嵌套数组

### 1.1 Array.prototype.flat

`flat()`方法会按照一个可指定的深度遍历递归**数组**，并将所有元素与遍历到的子数组中的元素合并为一个新数组返回。

**返回值**：一个新数组，不会改变旧数组。

#### 语法

```js
arr.flat([depth]);
```

- `depth` 是数组遍历的深度，默认是1。

举例：

```js
const arr = [1, 2, [[[[3, 4]]]]];
arr.flat();          // [1, 2, [[[3, 4]]]]
arr.flat(3);         // [1, 2, [3, 4]]
arr.flat(-1);        // [1, 2, [[[[3, 4]]]]]
arr.flat(Infinity);  // [1, 2, 3, 4]
```

#### 注意

- `flat()`会移除数组中的空项

```js
let arr = [1, 2, , , 3];
arr.flat();           // [1, 2, 3]
```

#### 手撕 flat

```js
  function customFlat(arr, depth = 1) {
    if(!Array.isArray(arr) || depth <= 0) {
        return arr;
    }
    return arr.reduce((pre, cur) => {
        if(Array.isArray(arr)) {
           return pre.concat(customFlat(cur, depth - 1));
        }
        return pre.concat(cur);
    }, []);
  }
```

#### 替换

- `reduce`与`concat`

```js
  let arr = [1, 2, [3, 4]];
  arr.reduce((arr, val) => arr.concat(val), []);
```

- `...` 扩展运算符与`concat`

```js
  let arr = [1, 2, [3, 4]];
  [].concat(...arr);
```

[更多替换方式请查看MDN](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FJavaScript%2FReference%2FGlobal_Objects%2FArray%2Fflat)

### 1.2 Array.prototype.flatMap

`flatMap()`方法首先使用映射函数映射数组（**深度值为1**）的每个元素，然后将结果压缩成一个新数组。

**返回值**：一个新数组，并且每个元素都是回调函数的结果。

#### 语法

```js
 arr.flatMap(function callback(currentVal[, index[, array]]) {

 }[, thisArg])
```

- callback: 可以生成一个新数组所调用的函数
  - currentVal: 当前数组在处理的元素
  - index: 可选，正在处理的元素索引
  - array: 可选，被调用的数组
- thisArg: 执行callback函数时使用的this值

举例：

```js
  let arr = ['My name', 'is', '', 'Lisa'];
  let newArr1 = arr.flatMap(cur => cur.split(' '));
  let newArr2 = arr.map(cur => cur.split(' '));
  console.log(newArr1); // ["My", "name", "is", "", "Lisa"]
  console.log(newArr2); // [["My", "name"], ["is"], [""], ["Lisa"]]
```

## 二、`Object.fromEntries`

`fromEntries()` 方法会把键值对列表转换成一个对象

**返回值**：一个新的对象

### 语法

```js
js
 Object.fromEntries(iterable)
```

- iterable: Array、Map等可迭代对象

举例：

```js
  let map = new Map([['a', 1], ['b', 2]]);
  let mapToObj = Object.fromEntries(map);
  console.log(mapToObj);  // {a: 1, b: 2}

  let arr = [['a', 1], ['b', 2]];
  let arrToObj = Object.fromEntries(arr);
  console.log(arrToObj);   // {a: 1, b: 2}

  let obj = {a: 1, b: 2};
  let newObj = Object.fromEntries(
    Object.entries(obj).map(
      ([key, val]) => [key, val * 2]
    )
  );
  console.log(newObj);   // {a: 2, b: 4}
```

## 三、`String.prototype.{trimStart, trimEnd}`

### 3.1 `String.prototype.trimStart`

`trimStart()` 方法用来删除字符串的开头的空白字符。

`trimLeft()` 是它的别名。

**返回值**：一个新的字符串，这个字符串左边的空格已经被去除掉了。

#### 语法

```js
  str.trimStart();
  str.trimLeft();
```

举例：

```js
  let str = '    a b cd  ';
  str.trimStart();   // 'a b cd  '
  str.trimLeft();    // 'a b cd  '
```

### 3.2 `String.prototype.trimEnd`

`trimEnd()` 方法用来删除字符串末尾的空白字符。

`trimRight()` 是它的别名

**返回值**：一个新的字符串，这个字符串右边的空格已经被去除了

#### 语法

```js
  str.trimEnd()
  str.trimRight()
```

举例：

```js
  let str = '    a b cd  ';
  str.trimEnd();                           // '    a b cd'
  str.trimRight();                         // '    a b cd'
```

## 四、`Symbol.prototype.description`

`description` 是一个只读属性

**返回值**：它返回Symbol对象的可选描述的字符串

### 语法

```js
  Symbol('myDescription').description;
  Symbol.iterator.description;
  Symbol.for('foo').description;
```

举例：

```js
  Symbol('foo').description;      // 'foo'
  Symbol().description;           // undefined
  Symbol.for('foo').description;  // 'foo'
```

## 五、`Optional catch binding`

可选的捕获绑定，允许省略catch绑定和它后面的圆括号

以前的用法：

```js
  try {

  } catch(err) {
    console.log('err', err);
  }
```

ES10 的用法：

```js
  try {

  } catch {

  }
```

## 六、`JSON.stringify()` 的增强力

`JSON.stringify()` 在 ES10 修复了对于一些超出范围的 Unicode 展示错误的问题，所以遇到 0xD800-0xDFF 之内的字符会因为无法编码成 UTF-8 进而导致显示错误。在 ES10 它会用转义字符的方式来处理这部分字符而非编码的方式，这样就会正常显示了。

```js
  JSON.stringify('😊'); // '"😊"'
```

## 七、修订 `Function.prototype.toString()`

以前的 toString 方法来自 `Object.prototype.toString()`，现在 的 `Function.prototype.toString()` 方法返回一个表示当前函数源代码的字符串。以前只会返回这个函数，不会包含空格、注释等。

```js
  function foo() {
      // es10新特性
    console.log('imooc')
  }
console.log(foo.toString());
// function foo() {
//     // es10新特性
//     console.log('imooc')
// }
```

# ES11

ES2020(ES11)新增了如下新特性👇：

- 空值合并运算符（Nullish coalescing Operator）
- 可选链 Optional chaining
- globalThis
- BigInt
- `String.prototype.matchAll()`
- `Promise.allSettled()`
- Dynamic import（按需 import）

## 一、空值合并运算符（Nullish coalescing Operator）

### 1.1 空值合并操作符（`??`）

**空值合并操作符**（`??`）是一个逻辑操作符，当左边的操作数为 `null` 或 `undefined` 的时候，返回其右侧操作符，否则返回左侧操作符。

```js
  undefined ?? 'foo'  // 'foo'
  null ?? 'foo'  // 'foo'
  'foo' ?? 'bar' // 'foo'
```

### 1.2 逻辑或操作符（`||`）

**逻辑或操作符**（`||`），会在左侧操作数为假值时返回右侧操作数，也就是说如果使用 `||` 来为某些变量设置默认值，可能会出现意料之外的情况。比如 0、''、NaN、false：

```js
  0 || 1  // 1
  0 ?? 1  // 0

  '' || 'bar'  // 'bar'
  '' ?? 'bar'  // ''

  NaN || 1  // 1
  NaN ?? 1  // NaN

  false || 'bar'  // 'bar'
  false ?? 'bar'  // false
```

### 1.3 注意

不可以将 `??` 与 AND（`&&`）OR（`||`）一起使用，会报错。

```js
  null || undefined ?? "foo"; // 抛出 SyntaxError
  true || undefined ?? "foo"; // 抛出 SyntaxError
```

## 二、可选链 Optional chaining

### 介绍

**可选链操作符**（`?.`）允许读取位于连接对象链深处的属性的值，而不必明确验证链中的每个引用都是否有效。`?.` 操作符的功能类似于`.`链式操作符，不同之处在于，在引用为 `null` 或 `undefined` 时不会报错，该链路表达式返回值为 `undefined`。

以前的写法：

```js
  const street = user && user.address && user.address.street;
  const num = user && user.address && user.address.getNum && user.address.getNum();
  console.log(street, num);
```

ES11 的写法：

```js
  const street2 = user?.address?.street;
  const num2 = user?.address?.getNum?.();
  console.log(street2, num2);
```

### 注意

可选链不能用于赋值：

```js
  let object = {};
  object?.property = 1; // Uncaught SyntaxError: Invalid left-hand side in assignment
```

## 三、globalThis

以前，在 Web 中，可以通过 `window`、`self` 取到全局对象，在 node.js 中，必须使用 `global`。

在松散模式下，可以在函数中返回 `this` 来获取全局对象，但是在严格模式和模块环境下，`this` 会返回 `undefined`。

以前要获取全局对象，可以定义一个函数：

```js
  const getGlobal = () => {
    if (typeof self !== 'undefined') {
        return self
    }
    if (typeof window !== 'undefined') {
        return window
    }
    if (typeof global !== 'undefined') {
        return global
    }
    throw new Error('无法找到全局对象')
  }

  const globals = getGlobal()
  console.log(globals)
```

现在 `globalThis` 提供了一个标准的方式来获取不同环境下的全局对象自身值。

## 四、BigInt

BigInt 是一种内置对象，用来创建比 2^53 - 1（Number 可创建的最大数字） 更大的整数。可以用来表示任意大的**整数**

### 如何定义一个 BigInt

- 在一个整数字面量后面加 n，例如 `10n`
- 调用函数 `BigInt()` 并传递一个整数值或字符串值，例如 `BigInt(10)`

### BigInt 的特点

- BigInt 不能用于 Math 对象中的方法；

- BigInt 不能与任何 Number 实例混合运算，两者必须转换成同一种类型。但是需要注意，BigInt 在转换成 Number 时可能会丢失精度。

- 当使用 BigInt 时，带小数的运算会被向下取整

- BigInt 和 Number 不是严格相等，但是宽松相等

```js
  0n === 0 // false
  0n == 0  // true
```

- BigInt 和 Number 可以比较

```js
  2n > 2   // false
  2n > 1   // true
```

- BigInt 和 Number 可以混在一个数组中排序

```js
  const mixed = [4n, 6, -12n, 10, 4, 0, 0n];
  mixed.sort();  // [-12n, 0, 0n, 10, 4n, 4, 6] 
```

- 被 Object 包装的 BigInt 使用 object 的比较规则进行比较，只用同一个对象比较时才相等

```js
  0n === Object(0n); // false
  Object(0n) === Object(0n); // false
  const o = Object(0n);
  o === o // true
```

### BigInt 的方法

#### BigInt.asIntN()

将 BigInt 值转换为一个 -2^(width-1) 与 2^(width-1) - 1 之间的有符号整数。

#### BigInt.asUintN()

将一个 BigInt 值转换为 0 与 2^(width) - 1 之间的无符号整数。

#### BigInt.prototype.toLocaleString()

返回此数字的 language-sensitive 形式的字符串。覆盖 `Object.prototype.toLocaleString()` 方法。

#### BigInt.prototype.toString()

返回以指定基数 (base) 表示指定数字的字符串。覆盖 `Object.prototype.toString()` 方法。

#### BigInt.prototype.valueOf()

返回指定对象的基元值。覆盖 `Object.prototype.valueOf()` 方法。

### 为什么会有 Bigint 的提案？

JavaScript 中 `Number.MAX_SAFE_INTEGER`表示最大安全数字，计算结果是 9007199254740991，即在这个数字范围内不会出现精度丢失（小数除外）。但是一旦超过这个范围，js 就会出现计算不准确的情况，这在大数计算的时候就不得不依靠一些第三方库进行解决，因此官方提出了 BigInt 来解决此问题。

## 五、`String.prototype.matchAll()`

返回一个包含所有匹配正则表达式的结果及分组捕获组的迭代器。

```js
  const regexp = /t(e)(st(\d?))/g;
  const str = 'test1test2';

  const array = [...str.matchAll(regexp)];
  console.log(array[0]);  // ["test1", "e", "st1", "1"]
  console.log(array[1]); // ["test2", "e", "st2", "2"]
```

## 六、`Promise.allSettled()`

类方法，返回一个在所有给定的 promise 都已经 fulfilled 或 rejected 后的 promise，并带有一个对象数组，每个对象表示对应的 promise 结果。

```js
  Promise.allSettled([
    Promise.resolve(33),
    new Promise((resolve) => setTimeout(() => resolve(66), 0)),
    99,
    Promise.reject(new Error("an error")),
  ]).then((values) => console.log(values)); 

// [
//   { status: 'fulfilled', value: 33 },
//   { status: 'fulfilled', value: 66 },
//   { status: 'fulfilled', value: 99 },
//   { status: 'rejected', reason: Error: an error }
// ]
```

## 七、Dynamic import（按需 import）

`import` 可以在需要的时候，再加载某个模块。

```js
  button.addEventListener('click', event => {
    import('./dialogBox.js')
    .then(dialogBox => {
      dialogBox.open();
    })
    .catch(error => {
      /* Error handling */
    })
  });
```

# ES12

ES 2021（ES12）新增了如下新特性👇：

- 逻辑运算符和赋值表达式（&&=，||=，??=）
- `String.prototype.replaceAll()`
- 数字分隔符
- `Promise.any`

## 一、逻辑运算符和赋值表达式（&&=，||=，??=）

### 1.1 &&=

逻辑与赋值运算符 `x &&= y` 等价于 `x && (x=y)`：意思是当 x 为真时，x = y。

```js
  let a = 1;
  let b = 0;

  a &&= 2;
  console.log(a); // 2

  b &&= 2;
  console.log(b);  // 0
```

### 1.2 ||=

逻辑或赋值运算符 `x ||= y` 等价于 `x || (x = y)`：意思是仅在 x 为 false 的时候，x = y。

```js
  const a = { duration: 50, title: '' };

  a.duration ||= 10;
  console.log(a.duration);  // 50

  a.title ||= 'title is empty.';
  console.log(a.title);  // "title is empty"
```

### 1.3 ??=

逻辑空赋值运算符 `x ??= y` 等价于 `x ?? (x = y)`：意思是仅在 x 为 null 或 undefined 的时候，x = y。

```js
  const a = { duration: 50 };

  a.duration ??= 10;
  console.log(a.duration);  // 50

  a.speed ??= 25;
  console.log(a.speed);  // 25
  ```

## 二、`String.prototype.replaceAll()`

返回一个新字符串，字符串中所有满足 pattern 的部分都会被 replacement 替换掉。原字符串保持不变。

- pattern 可以是一个字符串或 RegExp；
- replacement 可以是一个字符串或一个在每次被匹配被调用的函数。

```js
js
 'aabbcc'.replaceAll('b', '.');  // 'aa..cc'
```

使用正则表达式搜索值时，必须是全局的：

```js
 'aabbcc'.replaceAll(/b/, '.');  // TypeError: replaceAll must be called with a global RegExp

'aabbcc'.replaceAll(/b/g, '.');  // "aa..cc"
```

## 三、数字分隔符

ES12 允许 JavaScript 的数值使用下划线（_）作为分隔符，但是没有规定间隔的位数：

```js
  123_00
```

小数和科学记数法也可以使用分隔符：

```js
  0.1_23
  1e10_00
```

⚠️ 注意：

- 不能放在数值的最前面和最后面；
- 不能将两个及两个以上的分隔符连在一起；
- 小数点的前后不能有分隔符；
- 科学记数法里，e 或 E 前后不能有分隔符。

## 四、`Promise.any`

方法接受一组 Promise 实例作为参数，包装成一个新的 Promise 实例返回。

只要参数实例有一个变成 fulfilled 状态，包装实例就会变成 fulfilled 状态；如果所有参数实例都变成 rejected 状态，包装实例就会变成 rejected 状态。

```js
  const promise1 = () => {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve("promise1");
        //  reject("error promise1 ");
      }, 3000);
    });
  };
  const promise2 = () => {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve("promise2");
        // reject("error promise2 ");
      }, 1000);
    });
  };
  const promise3 = () => {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve("promise3");
        // reject("error promise3 ");
      }, 2000);
    });
  };

  Promise.any([promise1(), promise2(), promise3()])
    .then((first) => {
      // 只要有一个请求成功 就会返回第一个请求成功的
      console.log(first); // 会返回promise2
    })
    .catch((error) => {
      // 所有三个全部请求失败 才会来到这里
      console.log("error", error);
    });

  Promise.any([promise1(), promise2(), promise3()])
    .then((first) => {
      // 只要有一个请求成功 就会返回第一个请求成功的
      console.log(first); // 会返回promise2
    })
    .catch((error) => {
      // 所有三个全部请求失败 才会来到这里
      console.log("error", error);
    });
```