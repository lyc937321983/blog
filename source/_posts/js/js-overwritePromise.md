---
title: javascript手写Promise
date: 2022-11-04 17:37:38
tags: overwrite-promise
comment: 'valine'
categories: 
- js
---


# javascript手写Promise方法

## 1.Promise.resolve

### 简要回顾

1. `Promise.resolve(value) `方法返回一个以给定值解析后的[`Promise`](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FJavaScript%2FReference%2FGlobal_Objects%2FPromise) 对象。
2. 如果这个值是一个 promise ，那么将返回这个 promise ；
3. 如果这个值是thenable（即带有[`"then" `](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FJavaScript%2FReference%2FGlobal_Objects%2FPromise%2Fthen)方法），返回的promise会“跟随”这个thenable的对象，采用它的最终状态；否则返回的promise将以此值完成。

这是MDN上的解释，我们挨个看一下

1. `Promise.resolve`最终结果还是一个`Promise`，并且与`Promise.resolve(该值)`传入的值息息相关
2. 传入的参数可以是一个`Promise实例`，那么该函数执行的结果是直接将实例返回
3. 这里最主要需要理解跟随，可以理解成`Promise最终状态`就是这个thenable对象输出的**值**

------

**例子**

```javascript
// 1. 非Promise对象，非thenable对象
Promise.resolve(1).then(console.log) // 1

// 2. Promise对象成功状态
const p2 = new Promise((resolve) => resolve(2))

Promise.resolve(p2).then(console.log) // 2

// 3. Promise对象失败状态
const p3 = new Promise((_, reject) => reject('err3'))

Promise.resolve(p3).catch(console.error) // err3

// 4. thenable对象
const p4 = {
  then (resolve) {
    setTimeout(() => resolve(4), 1000)
  }
}
Promise.resolve(p4).then(console.log) // 4

// 5. 啥都没传
Promise.resolve().then(console.log) // undefined
```

### 源码实现

> 

```javascript
Promise.myResolve = function (value) {
  // 是Promise实例，直接返回即可
  if (value && typeof value === 'object' && (value instanceof Promise)) {
    return value
  }
  // 否则其他情况一律再通过Promise包装一下 
  return new Promise((resolve) => {
    resolve(value)
  })
}

// 测试一下，还是用刚才的例子
// 1. 非Promise对象，非thenable对象
Promise.myResolve(1).then(console.log) // 1

// 2. Promise对象成功状态
const p2 = new Promise((resolve) => resolve(2))

Promise.myResolve(p2).then(console.log) // 2

// 3. Promise对象失败状态
const p3 = new Promise((_, reject) => reject('err3'))

Promise.myResolve(p3).catch(console.error) // err3

// 4. thenable对象
const p4 = {
  then (resolve) {
    setTimeout(() => resolve(4), 1000)
  }
}
Promise.myResolve(p4).then(console.log) // 4

// 5. 啥都没传
Promise.myResolve().then(console.log) // undefined

```

**疑问**

从源码实现中，并没有看到对于`thenable`对象的特殊处理呀！其实确实也不需要在`Promise.resolve`中处理，真实处理的地方应该是在`Promise`构造函数中，如果你对这块感兴趣，马上就会写`Promise`的实现篇，期待你的阅读噢。

## 2.Promise.reject

### 简要回顾

> `Promise.reject() `方法返回一个带有拒绝原因的`Promise`对象。

```javascript
Promise.reject(new Error('fail'))
  .then(() => console.log('Resolved'), 
        (err) => console.log('Rejected', err))
// 输出以下内容        
// Rejected Error: fail
//    at <anonymous>:2:16        

```

### 源码实现

> reject实现相对简单，只要返回一个新的Promise，并且将结果状态设置为拒绝就可以

```javascript
Promise.myReject = function (value) {
  return new Promise((_, reject) => {
    reject(value)
  })
}

// 测试一下
Promise.myReject(new Error('fail'))
  .then(() => console.log('Resolved'), 
        (err) => console.log('Rejected', err))

// Rejected Error: fail
//    at <anonymous>:9:18
```

## 3.Promise.all

### 简要回顾

> `Promise.all()`方法用于将多个 Promise 实例，包装成一个新的 Promise 实例。**这个静态方法应该是面试中最常见的啦**

```javascript
const p = Promise.all([p1, p2, p3])
复制代码
```

最终`p`的状态由`p1`、`p2`、`p3`决定，分成两种情况。

（1）只有`p1`、`p2`、`p3`的状态都变成`fulfilled`，`p`的状态才会变成`fulfilled`，此时`p1`、`p2`、`p3`的返回值组成一个数组，传递给`p`的回调函数。

（2）只要`p1`、`p2`、`p3`之中有一个被`rejected`，`p`的状态就变成`rejected`，此时第一个被`reject`的实例的返回值，会传递给`p`的回调函数。

```javascript
const p1 = Promise.resolve(1)
const p2 = new Promise((resolve) => {
  setTimeout(() => resolve(2), 1000)
})
const p3 = new Promise((resolve) => {
  setTimeout(() => resolve(3), 3000)
})

const p4 = Promise.reject('err4')
const p5 = Promise.reject('err5')
// 1. 所有的Promise都成功了
const p11 = Promise.all([ p1, p2, p3 ])
	.then(console.log) // [ 1, 2, 3 ]
      .catch(console.log)
      
// 2. 有一个Promise失败了
const p12 = Promise.all([ p1, p2, p4 ])
	.then(console.log)
      .catch(console.log) // err4
      
// 3. 有两个Promise失败了，可以看到最终输出的是err4，第一个失败的返回值
const p13 = Promise.all([ p1, p4, p5 ])
	.then(console.log)
      .catch(console.log) // err4
```

### 源码实现

```javascript
Promise.myAll = (promises) => {
  return new Promise((rs, rj) => {
    // 计数器
    let count = 0
    // 存放结果
    let result = []
    const len = promises.length
    
    if (len === 0) {
      return rs([])
    }
    
    promises.forEach((p, i) => {
      // 注意有的数组项有可能不是Promise，需要手动转化一下
      Promise.resolve(p).then((res) => {
        count += 1
        // 收集每个Promise的返回值 
        result[ i ] = res
        // 当所有的Promise都成功了，那么将返回的Promise结果设置为result
        if (count === len) {
          rs(result)
        }
        // 监听数组项中的Promise catch只要有一个失败，那么我们自己返回的Promise也会失败
      }).catch(rj)
    })
  })
}

// 测试一下
const p1 = Promise.resolve(1)
const p2 = new Promise((resolve) => {
  setTimeout(() => resolve(2), 1000)
})
const p3 = new Promise((resolve) => {
  setTimeout(() => resolve(3), 3000)
})

const p4 = Promise.reject('err4')
const p5 = Promise.reject('err5')
// 1. 所有的Promise都成功了
const p11 = Promise.myAll([ p1, p2, p3 ])
	.then(console.log) // [ 1, 2, 3 ]
      .catch(console.log)
      
// 2. 有一个Promise失败了
const p12 = Promise.myAll([ p1, p2, p4 ])
	.then(console.log)
      .catch(console.log) // err4
      
// 3. 有两个Promise失败了，可以看到最终输出的是err4，第一个失败的返回值
const p13 = Promise.myAll([ p1, p4, p5 ])
	.then(console.log)
      .catch(console.log) // err4
// 与原生的Promise.all返回是一致的    
```

## 4.Promise.allSettled

### 简要回顾

> 有时候，我们希望等到一组异步操作都结束了，不管每一个操作是成功还是失败，再进行下一步操作。显然`Promise.all`(其只要是一个失败了，结果即进入失败状态)不太适合，所以有了`Promise.allSettled`

> ```
> Promise.allSettled()`方法接受一个数组作为参数，数组的每个成员都是一个 Promise 对象，并返回一个新的 Promise 对象。只有等到参数数组的所有 Promise 对象都发生状态变更（不管是`fulfilled`还是`rejected`），返回的 Promise 对象才会发生状态变更,一旦发生状态变更，状态总是`fulfilled`，不会变成`rejected
> ```

还是以上面的例子为例， 我们看看与`Promise.all`有什么不同

```javascript
const p1 = Promise.resolve(1)
const p2 = new Promise((resolve) => {
  setTimeout(() => resolve(2), 1000)
})
const p3 = new Promise((resolve) => {
  setTimeout(() => resolve(3), 3000)
})

const p4 = Promise.reject('err4')
const p5 = Promise.reject('err5')
// 1. 所有的Promise都成功了
const p11 = Promise.allSettled([ p1, p2, p3 ])
	.then((res) => console.log(JSON.stringify(res, null,  2)))

// 输出 
/*
[
  {
    "status": "fulfilled",
    "value": 1
  },
  {
    "status": "fulfilled",
    "value": 2
  },
  {
    "status": "fulfilled",
    "value": 3
  }
]
*/
      
// 2. 有一个Promise失败了
const p12 = Promise.allSettled([ p1, p2, p4 ])
	.then((res) => console.log(JSON.stringify(res, null,  2)))
        
// 输出 
/*
[
  {
    "status": "fulfilled",
    "value": 1
  },
  {
    "status": "fulfilled",
    "value": 2
  },
  {
    "status": "rejected",
    "reason": "err4"
  }
]
*/
      
// 3. 有两个Promise失败了
const p13 = Promise.allSettled([ p1, p4, p5 ])
	.then((res) => console.log(JSON.stringify(res, null,  2)))
        
// 输出 
/*
[
  {
    "status": "fulfilled",
    "value": 1
  },
  {
    "status": "rejected",
    "reason": "err4"
  },
  {
    "status": "rejected",
    "reason": "err5"
  }
]
*/
```

**可以看到：**

1. 不管是全部成功还是有部分失败，最终都会进入`Promise.allSettled`的`.then`回调中
2. 最后的返回值中，成功和失败的项都有`status`属性，成功时值是`fulfilled`，失败时是`rejected`
3. 最后的返回值中，成功含有`value`属性，而失败则是`reason`属性

### 源码实现

```javascript
Promise.myAllSettled = (promises) => {
  return new Promise((rs, rj) => {
    let count = 0
    let result = []
    const len = promises.length
    // 数组是空的话，直接返回空数据
    if (len === 0) {
      return resolve([])
    }

    promises.forEach((p, i) => {
      Promise.resolve(p).then((res) => {
        count += 1
        // 成功属性设置 
        result[ i ] = {
          status: 'fulfilled',
          value: res
        }
        
        if (count === len) {
          rs(result)
        }
      }).catch((err) => {
        count += 1
        // 失败属性设置 
        result[i] = { 
          status: 'rejected', 
          reason: err 
        }

        if (count === len) {
          rs(result)
        }
      })
    })
  })
}

// 测试一下
const p1 = Promise.resolve(1)
const p2 = new Promise((resolve) => {
  setTimeout(() => resolve(2), 1000)
})
const p3 = new Promise((resolve) => {
  setTimeout(() => resolve(3), 3000)
})

const p4 = Promise.reject('err4')
const p5 = Promise.reject('err5')
// 1. 所有的Promise都成功了
const p11 = Promise.myAllSettled([ p1, p2, p3 ])
	.then((res) => console.log(JSON.stringify(res, null,  2)))

// 输出 
/*
[
  {
    "status": "fulfilled",
    "value": 1
  },
  {
    "status": "fulfilled",
    "value": 2
  },
  {
    "status": "fulfilled",
    "value": 3
  }
]
*/
      
// 2. 有一个Promise失败了
const p12 = Promise.myAllSettled([ p1, p2, p4 ])
	.then((res) => console.log(JSON.stringify(res, null,  2)))
        
// 输出 
/*
[
  {
    "status": "fulfilled",
    "value": 1
  },
  {
    "status": "fulfilled",
    "value": 2
  },
  {
    "status": "rejected",
    "reason": "err4"
  }
]
*/
      
// 3. 有两个Promise失败了
const p13 = Promise.myAllSettled([ p1, p4, p5 ])
	.then((res) => console.log(JSON.stringify(res, null,  2)))
        
// 输出 
/*
[
  {
    "status": "fulfilled",
    "value": 1
  },
  {
    "status": "rejected",
    "reason": "err4"
  },
  {
    "status": "rejected",
    "reason": "err5"
  }
]
*/
```

## 5.Promise.race

### 简单回顾

`Promise.race()`方法同样是将多个 Promise 实例，包装成一个新的 Promise 实例。

```
const p = Promise.race([p1, p2, p3])
复制代码
```

只要`p1`、`p2`、`p3`之中有一个实例率先改变状态，`p`的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给`p`的回调函数。

```javascript
const p1 = new Promise((resolve, reject) => {
  setTimeout(resolve, 500, 1)
})

const p2 = new Promise((resolve, reject) => {
  setTimeout(resolve, 100, 2)
})

Promise.race([p1, p2]).then((value) => {
  console.log(value) // 2
})

Promise.race([p1, p2, 3]).then((value) => {
  console.log(value) // 3
})
```

### 源码实现

> 聪明的你一定马上知道该怎么实现了，只要了解哪个实例先改变了，那么`Promise.race`就跟随这个结果，那么就可以写出以下代码

```javascript
Promise.myRace = (promises) => {
  return new Promise((rs, rj) => {
    promises.forEach((p) => {
      // 对p进行一次包装，防止非Promise对象
      // 并且对齐进行监听，将我们自己返回的Promise的resolve，reject传递给p，哪个先改变状态，我们返回的Promise也将会是什么状态
      Promise.resolve(p).then(rs).catch(rj)
    })
  })
}

// 测试
const p1 = new Promise((resolve, reject) => {
  setTimeout(resolve, 500, 1)
})

const p2 = new Promise((resolve, reject) => {
  setTimeout(resolve, 100, 2)
})

Promise.myRace([p1, p2]).then((value) => {
  console.log(value) // 2
})

Promise.myRace([p1, p2, 3]).then((value) => {
  console.log(value) // 3
})
```