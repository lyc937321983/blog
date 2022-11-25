---
title: vue2和vue3的双向数据绑定
date: 2022-01-07 16:15:22
tags: v-model
comment: 'valine'
categories: 
- vue

---


# vue2和vue3的双向数据绑定

## 1.vue2

#### 基于Object.defineProperty()实现

> Object.defineProperty() 在一个对象上定义一个新的属性，或者修改这个对象上已经存在的属性

```js
Object.defineProperty(obj, prop, desc)
```

1. obj: 目标对象
2. prop: 对象的key
3. desc: 属性描述

##### desc 参数为一个对象，里面的属性是

```js
let Person = {}
let backup = null
Object.defineProperty(Person, 'name', {
    value: 'Jack', // key 的值
    writable: true, // 是否可写，默认false
    enumerable: true, // 是否可枚举，是否可for in
    configurable: ture, // 是否可配置，是否可删除等，默认fasle
    get: function() { // 获取数据方法
        return backup
    },
    set: function(newVal) {  // 更改方法，接受参数为新值
        backup = newVal
    }
})
```

> Object.defineProperty(obj, prop, desc)的vue双向数据绑定

```js
function vue() {
    this.$data = {
        testData: 1
    }
    this.el = docunment.getElementById('app')
    this.virtualDom = ''
    this.observer(this.$data)
    this.render()
}
vue.prototype.observer = function(data) {
    let self = this;
    let backup = null;
    for(let key in data) {
        backup = data[key] // 先备份数据
        if (typeof backup === 'object') {
            this.observer(backup)
        } else {
            Object.defineProperty(data, key, {
                get: function() {
                    // 依赖收集
                    return backup // 一定要return出去才可以
                },
                set: function(newVal) {
                    backup = newVal
                    self.render() // 刷新视图
                }
            })
        }
    }
}
vue.prototype.render = functin() {
    this.virtualDom = 'a test vue' + this.$data.testData
    this.el.innerHtml = this.virtualDom
}
```

## 2.vue3 基于Proxy

##### 与Object.defineProperty(obj, prop, desc)方式相比有以下优势：

1. 丢掉麻烦的备份数据
2. 省去for in 循环
3. 可以监听数组变化
4. 代码更简化

```js
function vue() {
    this.$data = {
        testData: 1
    }
    this.el = docunment.getElementById('app')
    this.virtualDom = ''
    this.observer(this.$data)
    this.render()
}
vue.prototype.observer = function(data) {
    let self = this;
    this.$data = new Proxy(this.data, {
        get: function(target, key, vceiver) {
            return target[key]
        },
        set: function(target, key, value, vceiver) {
            target[key] = value
            slef.render()
        }
    })
}
vue.prototype.render = functin() {
    this.virtualDom = 'a test vue' + this.$data.testData
    this.el.innerHtml = this.virtualDom
}
```