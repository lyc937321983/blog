---
title: JS手写发布订阅设计模式(Event Bus)
date: 2025-8-2 14:15:22
tags: Event Bus
comment: 'valine'
categories: 
- js
---

# 手写发布订阅设计模式

### 什么是发布订阅设计模式？

***打个比方：***

- 想象一个 **微信公众号平台**。
- 写文章的人就是 **发布者 (Publisher)** 。
- 看文章的人就是 **订阅者 (Subscriber)** 。
- 微信公众号平台本身就是 **事件通道/消息代理/事件总线 (Event Channel/Message Broker/Event Bus)** 。
- 作者写好文章后，不是直接发给读者，而是 **发布 (Publish)** 到公众号平台上，说“我写了一篇关于‘设计模式’的文章”。
- 读者对“设计模式”感兴趣，就 **订阅 (Subscribe)** 了这个主题（Topic）。
- 平台负责 **把消息从发布者传递给订阅者**。当有新的“设计模式”文章发布时，平台会自动推送给所有订阅了该主题的读者。
- 关键点：作者 **不知道** 谁订阅了他的文章；读者 **也不知道** 文章具体是谁写的（除非作者署名）。他们只和平台打交道。

> 简单来说：读者看到自己喜欢的文章，则会点击订阅，平台收到后将订阅消息先存储起来，等到发布者发布了该类文章，则平台找到喜欢该文章的订阅消息，将其执行，也就是向读者推荐该文章。

### 那么要如何实现发布订阅设计模式呢？

底层逻辑：定义一个**EventEmitter**类，该类内部定义了**订阅函数on**，**发布函数emit**以及**enventList对象**（用于存储订阅消息的），再创建一个EventEmitter类的**_event实例**（事件总线），每当有订阅信息，_event则调用on方法将回调函数（订阅者）与特定事件名称关联起来，存储在内部的 eventList 对象中。只要等到发布文章时，_event调用emit方法查找对应事件的所有回调函数并执行，实现了发布者与订阅者的解耦 。

```js
class EventEmitter{
    constructor(){
        //定义eventList对象，用于存储各类文章的订阅事件
        this.eventList={}
    }
    //订阅函数，eventName未订阅的书籍类型，cb订阅者的回调函数，发布时调用
    on(eventName,cb){//订阅事件
        //当对象内不存在该类订阅书籍，则添加一个新数组进行存储
        if(!this.eventList[eventName]){
            this.eventList[eventName]=[]
        }
        //将书籍类型设为键，订阅者的回调函数加入到该类的数组中
        this.eventList[eventName].push(cb)
    }
    //发布函数，当发布者发布文章时，事件总线则调用该函数，执行所有该类文章的回调函数
    //eventName表示要发布的文章的类别
    emit(eventName){//发布事件
        //查找订阅消息对象中有无该类文章的订阅消息
        if(this.eventList[enventName]){
        //将订阅的事件回调函数复制一份，避免直接操作订阅的事件回调函数数组
            const handlers=this.eventList[eventName].slice()
            //遍历订阅的事件回调函数数组，执行每个回调函数
            handlers.forEach(item=>{
                item()
            })
        }
    }
}
//创建事件总线_event
let _event=new EventEmitter()
//订阅者kang，
function kang(){
    console.log('小康收到文学类文章')
}
//订阅者liu
function liu(){
    console.log('小刘收到艺术类文章')
}
//订阅者cheng
function cheng(){
    console.log('小成收到科学类文章')
}
_event.on('wenxue',kang)//订阅事件'wenxue'，订阅者kang
_event.on('kexue',cheng)//订阅事件'kexue'，订阅者cheng
_event.emit('wenxue')//发布事件'wenxue'-->订阅者kang执行 
_event.emit('kexue')//发布事件'hasCar'-->订阅者cheng执行 
```

**简单总结**：事件总线调用on方法将小康和小刘的订阅消息分别存储到文学和艺术类订阅数组内，当发布者发布了文学类书籍时，事件总线则会调用emit方法，将该书籍推荐给小康，小康则会收到文学类书籍，但小刘收不到，小刘只有当艺术类书籍发布时才会收到。

### 如何实现取消订阅呢？

**大体思路**：就是在EventEmitter类内再**添加一个off方法用于取消订阅**，当有读者想取消订阅时，事件总线则调用off方法，将该读者的订阅消息从其订阅的那类书籍的数组内删除即可

```js
//eventName为要取消哪类书籍的订阅，cb为订阅者的回调函数
off(eventName,cb){//取消订阅事件
    //查找该读者是否订阅过该类书籍
    if(!this.eventList[eventName].find(item=>item===cb)){
        return 
    }
    const handlers=this.eventList[eventName]
    //将订阅的事件回调函数数组中，与取消订阅的事件回调函数不同的回调函数保留，相同的回调函数删除
    this.enventList[eventName]=handlers.filter(item=>item!==cb)
}
_event.off('wenxue',kang)//表示订阅者kang取消了文学类书籍的订阅事件
```

### 如何实现单次订阅呢?

**大体思路：**

1. 创建**包装函数wrap**（形成闭包环境）

- 闭包捕获：原始回调`cb`、事件名称`eventName`、当前`this`上下文

1. wrap函数内部逻辑：

- 执行原始回调`cb()`
- 调用`this.off(eventName, wrap)`**移除自身**

1. 使用`this.on(eventName, wrap)`将wrap函数添加到订阅消息数组内

**当事件触发时：**

- 执行wrap函数
- 先执行原始回调
- 随后移除wrap自身
- 实现**只触发一次**的效果

```js
    once(eventName,cb){//单次订阅
        //创建wrap函数，具有闭包特性，wrap函数会记住当前的cb函数以及eventName和this值，当其被调用时引用
        const wrap=()=>{
            cb()
            this.off(eventName,wrap)
        }
        //将wrap添加到订阅消息内，发布时调用
        this.on(eventName,wrap)
    }
    _event.once('yishu',liu)//单次订阅事件'yishu'，订阅者liu
    _event.emit('yishu')//发布事件'yishu'-->订阅者liu执行 
    _event.emit('yishu')//发布事件'yishu'-->订阅者liu不执行 
```