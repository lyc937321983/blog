---
title: javascript对象数组去重
date: 2022-08-08 11:28:38
tags: 去重
comment: 'valine'
categories: 
- js
---

# javascript 对象数组去重

### 1.双重for循环

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

实现倒是实现了，但是看着这么一大坨......,不行啊，咱写代码要优雅

### 2.forEach搭配findindex

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

### 3.filter搭配findIndex

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

#### 4.forEach搭配some

在总结完上述三个方法之后，我就在想了：数组去重，如果这个数组是个基本数据类型的数组，我们只要遍历一层，循环体里面只要配合indexOf、includes等方法，就可以找出符合条件的值了，代码如下：

```javascript
    let arr = [ 1, 1, 1, "1", "lsm", "52", 2, 81, 2, 81]
    let newArr = []
    arr.map((item, index) => {
      //!newArr.includes(item) && newArr.push(item)
      newArr.indexOf(arr1[i]) === -1 && newArr.push(arr1[i])
    })
    console.log('newArr--', newArr)
```

判断一个数组中有没有符合条件的值，只要数组中有一项符合条件返回true，但是不会终止循环，可以使用return终止循环（该例中并没用到该特性）

#### 5.filter和find

按照上面的总结，想进行引用类型数组去重，得进行双重for循环，内层循环要有一个具体的返回值。外层循环用来提供去重的对象，可以使用forEach，map等单纯提供遍历的方法，但是需要重新定义一个新的数组接收符合条件的对象。也可以使用filter，根据条件，直接返回一个新的数组，不需要重新定义一个新的数组，也不用进行push操作。和findIndex方法类似的还有find方法，代码如下

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

#### 6.map结合some | find | findexIndex

可能大家看到这里就在吐槽了，上面总结的时候为什么，forEach和Filter要混着举例。但是其实写这篇文章，最终目的并不是给大家例举出各种情况，而是想让大家了解引用数据类型数组的去重思路。数组的遍历方法有很多，可以有很多中搭配方式实现去重，只是单纯靠背的话，总有一天会忘，就像我这只菜菜，只会个for循环......

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