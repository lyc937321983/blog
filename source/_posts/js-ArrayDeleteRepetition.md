---
title: javascript对象数组去重
date: 2022-08-08 11:28:38
tags: 去重
comment: 'valine'
categories: 
- js
---

# javascript 对象数组去重

### 1.使用set和map结合

```js
let jsonArray = [  
  { id: 1, name: 'John' },  
  { id: 2, name: 'Mary' },  
  { id: 3, name: 'Bob' },  
  { id: 1, name: 'John' },  
  { id: 2, name: 'Mary' },  
];  
  
let uniqueArray = [...new Set(jsonArray.map(JSON.stringify))].map(JSON.parse);  
  
console.log(uniqueArray);
```

​		首先，使用`Array.prototype.map`和`JSON.stringify`将数组中的每个对象转换为字符串。
​		然后，我们使用`Set`对象去除重复的字符串。
​		最后，我们使用`Array.prototype.map`和`JSON.parse`将结果转换回对象数组

### 2.双重for循环

#### i.对比id属性

```js
let arr = [
    {id: 1, name: 'kuye'},
    {id: 2, name: 'gucheng'},
    {id: 1, name: 'kuye'}
]
for (let i = 0; i < arr.length; i++) {
    for (let j = i + 1; j < arr.length; j++) {
        if (arr[i].id === arr[j].id) {
            arr.splice(j, 1)
            j--
        }
    }
}
console.log('arr--', arr)
```

#### ii.使用JSON.stringify转字符串，在对比

```js
let jsonArray = [  
  { id: 1, name: 'John' },  
  { id: 2, name: 'Mary' },  
  { id: 3, name: 'Bob' },  
  { id: 1, name: 'John' },  
  { id: 2, name: 'Mary' },  
];  
  
let uniqueArray = [];  
  
for (let i = 0; i < jsonArray.length; i++) {  
  let isDuplicate = false;  
  for (let j = 0; j < uniqueArray.length; j++) {  
    if (JSON.stringify(jsonArray[i]) === JSON.stringify(uniqueArray[j])) {  
      isDuplicate = true;  
      break;  
    }  
  }  
  if (!isDuplicate) {  
    uniqueArray.push(jsonArray[i]);  
  }  
}  
  
console.log(uniqueArray);
```

​		使用两个嵌套的 for 循环。外部循环遍历原始数组，内部循环遍历新数组。如果原始数组中的当前元素与新数组中的任何元素相同，那么我们将 `isDuplicate` 设置为 `true` 并跳出内部循环。如果 `isDuplicate` 为 `false`，则我们将当前元素添加到新数组中。最后，新数组将只包含没有重复的数据。

### 3.forEach搭配findindex

```js
let arr1 = [
    {id: 1, name: 'kuye'},
    {id: 2, name: 'gucheng'},
    {id: 1, name: 'kuye'}
]
let newArr1 = []
arr1.forEach((item, index) => {
    arr1.findIndex(el => el.id == item.id) == index && newArr1.push(item)
})
console.log('newArr1', newArr1)
```

找到遍历的数组中的第一个符合判断条件的值，并返回该值对应的索引，停止遍历

### 4.filter搭配findIndex

作为一名优秀的程序员，只会两种去重方式怎么行，继续去粘，，，继续总结...

```js
let arr2 = [
    {id: 1, name: 'kuye'},
    {id: 2, name: 'gucheng'},
    {id: 1, name: 'kuye'}
]
arr2 = arr2.filter((item, index) => {
    return arr2.findIndex(el => el.id == item.id) == index
})
console.log('arr3--', arr2)
```

实现数组过滤。判断filter回调函数中的条件是否为true，如果为true，返回该遍历项，最终包装到一个数组中统一返回

### 5.forEach搭配some

​		在总结完上述三个方法之后，我就在想了：数组去重，如果这个数组是个基本数据类型的数组，我们只要遍历一层，循环体里面只要配合indexOf、includes等方法，就可以找出符合条件的值了，代码如下：

```javascript
let arr = [ 1, 1, 1, "1", "lsm", "52", 2, 81, 2, 81]
let newArr = []
arr.map((item, index) => {
    //!newArr.includes(item) && newArr.push(item)
    newArr.indexOf(arr1[i]) === -1 && newArr.push(arr1[i])
})
console.log('newArr--', newArr)
```

​		判断一个数组中有没有符合条件的值，只要数组中有一项符合条件返回true，但是不会终止循环，可以使用return终止循环（该例中并没用到该特性）

### 6.filter和find

​		按照上面的总结，想进行引用类型数组去重，得进行双重for循环，内层循环要有一个具体的返回值。外层循环用来提供去重的对象，可以使用forEach，map等单纯提供遍历的方法，但是需要重新定义一个新的数组接收符合条件的对象。也可以使用filter，根据条件，直接返回一个新的数组，不需要重新定义一个新的数组，也不用进行push操作。和findIndex方法类似的还有find方法，代码如下

```javascript
let arr4 = [
      {id: 1, name: 'kuye'},
      {id: 2, name: 'gucheng'},
      {id: 1, name: 'kuye'}
    ]
    arr4 = arr4.filter(item => {
      return arr4.find(el => el.id == item.id) === item
    })
    console.log('arr4', arr4)
```

找出符合判断条件的第一项，并进行返回

### 7.map结合some | find | findexIndex

**最终总结：**

**1. 实现引用类型数组去重，主要靠双重循环。**

**2. 外层的循环可以是forEach、map这种方法，单纯的给内层循环提供去重对象。这要我们在最外面定义一个新的数组，用来存放符合条件的数组。也可以用filter方法，根据filter的特性返回符合条件的数组，不用自定义新数组。**

**3. 内层的函数实现去重，并且内层的函数要有一个有具体含义的返回值，用于外层函数的判断。可以是some，findIndex，find等。**

具体情况是不是与上述总结一直呢。我们最后使用map和some、find、findexIndex再来证明一遍

```js
    let arr5 = [
      {id: 1, name: 'kuye'},
      {id: 2, name: 'gucheng'},
      {id: 1, name: 'kuye'}
    ]
    let newArr5 = []
    arr5.map((item, index) => {
      // !newArr5.some(el => el.id == item.id) && newArr5.push(item)
      // arr5.findIndex(el => el.id === item.id) === index && newArr5.push(item)
      arr5.find(el => el.id === item.id) === item && newArr5.push(item)
    })
    console.log('arr5', newArr5)
```