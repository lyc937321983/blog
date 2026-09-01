---
title: javascript宏任务和微任务
date: 2022-02-21 14:57:22
tags: 宏任务和微任务
comment: 'valine'
categories: 
- js
---

# JS宏任务和微任务

### 一.前言

了解宏任务和微任务之前，我们先了解一下事件循环和任务队列，还要需要知道的专业名词术语：

```text
synchronous：同步任务
asynchronous：异步任务
execution context stack：执行栈
heap：堆
stack：栈
```

#### 事件循环（Event Loop）

​		JavaScript 语言的一大特点就是单线程，同一个时间只能做一件事。为了协调事件、用户交互、脚本、UI 渲染和网络处理等行为，防止主线程的不阻塞，Event Loop 的方案应用而生。Event Loop 包含两类：一类是基于 [Browsing Context]，一种是基于 [Worker]。二者的运行是独立的，也就是说，每一个 JavaScript 运行的"线程环境"都有一个独立的 Event Loop，每一个 Web Worker 也有一个独立的 Event Loop。

#### 任务队列（task queue/callback queue）

​		根据规范，事件循环是通过任务队列的机制来进行协调的。一个 Event Loop 中，可以有一个或者多个任务队列(task queue)，一个任务队列便是一系列有序任务(task)的集合；每个任务都有一个任务源(task source)，源自同一个任务源的 task 必须放到同一个任务队列，从不同源来的则被添加到不同队列。setTimeout/Promise 等API便是任务源，而进入任务队列的是他们指定的具体执行任务。

### 二.宏任务和微任务

#### 1.宏任务（macro-task）

​		每次执行栈执行的代码就是一个宏任务（包括每次从事件队列中获取一个事件回调并放到执行栈中执行）

##### 宏任务包含：

```text
script(整体代码)
setTimeout
setInterval
I/O
UI交互事件
postMessage
MessageChannel
setImmediate(Node.js 环境)
```

#### 2.微任务（micro-task）

​		在当前 task 执行结束后立即执行的任务。也就是说，在当前task任务后，下一个task之前，在渲染之前。

##### 微任务包含：

```text
Promise.then
Object.observe
MutationObserver
process.nextTick(Node.js 环境)
```

#### 3.运行机制

JS引擎常驻于内存中，等待宿主将JS代码或函数传递给它。也就是等待宿主环境分配宏观任务，反复等待 - 执行即为事件循环。 

Event Loop中，每一次循环称为tick，每一次tick的任务如下：

> 执行栈选择最先进入队列的宏任务（一般都是script），执行其同步代码直至结束；
> 检查是否存在微任务，有则会执行至微任务队列为空；
> 如果宿主为浏览器，可能会渲染页面；
> 开始下一轮tick，执行宏任务中的异步代码（setTimeout等回调）。

#### 4.实例代码

```js
console.log('start')

setTimeout(() => {
  console.log('setTimeout')
}, 0)

new Promise((resolve) => {
  console.log('promise')
  resolve()
})
  .then(() => {
    console.log('then1')
  })
  .then(() => {
    console.log('then2')
  })

console.log('end')

//运行结果：
//start 
//promise
//end
//then1
//then2
//setTimeout

```

