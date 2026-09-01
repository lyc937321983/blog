---
title: Vue中的$nextTick是怎么实现的
date: 2022-11-04 14:37:38
tags: nextTick
comment: 'valine'
categories: 
- vue
---

# Vue中的$nextTick是怎么实现的

### 1.说明

vue官方对nextTick的说明：

​			https://v2.cn.vuejs.org/v2/api/#vm-nextTick

### 2.简单示例

先看一个简单的问题：

```vue
<template>
  <div @click="handleClick" ref="div">{{ text }}</div
</template>
<script>
  export default {
    data() {
      return {
        text: 'old'
      }
    },
    methods: {
      handleClick() {
        this.text = 'new'
        console.log(this.$refs.div.innerText)
      }
    }
  }
</script>
```

此时打印的结果是什么呢？是 `'old'`。如果想让它打印 `'new'`，使用 `nextTick` 稍加改造就可以

```javascript
this.$nextTick(() => {
  console.log(this.$refs.div.innerText)
})
```

### 3.github源码实现

但是你想过它内部是怎么实现的么，和我们写 `setTimeout` 有什么区别呢？

因为平时工作使用的是Vue2，所以我就以Vue2的最新版本2.6.14为例进行分析，Vue3的实现应该也是大同小异。

源码地址：[github.com/vuejs/vue/b…](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fvuejs%2Fvue%2Fblob%2F2.6%2Fsrc%2Fcore%2Futil%2Fnext-tick.js)

为了方便阅读我删掉了注释，只关注最重要的实现

```js
if (typeof Promise !== 'undefined' && isNative(Promise)) {
  const p = Promise.resolve()
  timerFunc = () => {
    p.then(flushCallbacks)
    if (isIOS) setTimeout(noop)
  }
  isUsingMicroTask = true
} else if (!isIE && typeof MutationObserver !== 'undefined' && (
  isNative(MutationObserver) || MutationObserver.toString() === '[object MutationObserverConstructor]'
)) {
  let counter = 1
  const observer = new MutationObserver(flushCallbacks)
  const textNode = document.createTextNode(String(counter))
  observer.observe(textNode, {
    characterData: true
  })
  timerFunc = () => {
    counter = (counter + 1) % 2
    textNode.data = String(counter)
  }
  isUsingMicroTask = true
} else if (typeof setImmediate !== 'undefined' && isNative(setImmediate)) {
  timerFunc = () => {
    setImmediate(flushCallbacks)
  }
} else {
  timerFunc = () => {
    setTimeout(flushCallbacks, 0)
  }
}
```

### 4.分开解析源码

先大概扫一遍可知 `$nextTick` 主要是通过微任务来实现的，其实在2.5版本中，是采用宏任务与微任务相结合的方式实现的，但因为在渲染和事件处理中一些比较怪异的行为（感兴趣的话可以看下issue），所以最终统一采用了微任务。

#### 1.先看第一块：

```js
if (typeof Promise !== 'undefined' && isNative(Promise)) {
  const p = Promise.resolve()
  timerFunc = () => {
    p.then(flushCallbacks)
    if (isIOS) setTimeout(noop)
  }
  isUsingMicroTask = true
}
```

如果可以使用 `Promise` ，就采用 `promise.then` 的方式去执行回调，将任务在下一个tick执行。但是其中 `if (isIOS) setTimeout(noop)` 这句话是在做什么呢？在iOS >= 9.3.3的UIWebView中，定义的回调函数通过 `Promise` 的方式推到微任务队列后，队列不刷新，需要靠 `setTimeout` 来强制更新一下，`noop` 就是一个空函数。

#### 2.再看第二块：

```js
else if (!isIE && typeof MutationObserver !== 'undefined' && (
  isNative(MutationObserver) || MutationObserver.toString() === '[object MutationObserverConstructor]'
)) {
  let counter = 1
  const observer = new MutationObserver(flushCallbacks)
  const textNode = document.createTextNode(String(counter))
  observer.observe(textNode, {
    characterData: true
  })
  timerFunc = () => {
    counter = (counter + 1) % 2
    textNode.data = String(counter)
  }
  isUsingMicroTask = true
}
```

如果不能用 `Promise` 就降级使用 `MutationObserver`。创建了一个文本节点，并通过 `observer` 去观察文本节点的变化。 `characterData: true` 这个配置就是当文字变化的时候就会执行回调。`(counter + 1) % 2` 会使文本节点的文字在 `0` 、 `1` 、 `0` 、 `1`之间不同变化，这样就会被 `observer` 观察到。`MutationObserver` 也是微任务。

#### 3.然后是第三块：

```js
else if (typeof setImmediate !== 'undefined' && isNative(setImmediate)) { 
  timerFunc = () => { 
    setImmediate(flushCallbacks) 
  } 
}
```

当微任务都不被支持时，就要使用宏任务了。其实大多数情况下都不会走到这里，因为 `setImmediate` 并没有成为正式的标准，并且兼容性很差。

#### 4.最后是第四块：

```js
else {
  timerFunc = () => {
    setTimeout(flushCallbacks, 0)
  }
}
```

最后在所有方案都行不通时，只能采用 `setTimeout` 的方式。之所以有第三块是因为虽然都是宏任务，但是 `setImmediate` 会比 `setTimeout` 快，所以MDN上才会说 `setTimeout(fn, 0)` 不能成为 `setImmediate` 的polyfill。就像作者在注释中写的那样：它仍然是比 `setTimeout` 更好的选择。