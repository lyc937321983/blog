---
title: javascript 策略模式
date: 2024-07-16 20:03:22
tags: 策略模式
comment: 'valine'
categories: 
- js
---

# js 策略模式
 策略模式是一种简单却常用的设计模式，它的应用场景非常广泛。
 我们先了解下策略模式的概念，再通过代码示例来更清晰的认识它。

策略模式由两部分构成：一部分是封装不同策略的策略组，另一部分是 Context。
通过组合和委托来让 Context 拥有执行策略的能力，从而实现可复用、可扩展和可维护，并且避免大量复制粘贴的工作。

### 1、举例一


```js
const strategies = {
    "high": function (workHours) {
        return workHours * 25
    },
    "middle": function (workHours) {
        return workHours * 20
    },
    "low": function (workHours) {
        return workHours * 15
    },
}

const calculateSalary = function (workerLevel, workHours) {
    return strategies[workerLevel](workHours)
}

console.log(calculateSalary('high', 10)) // 250
console.log(calculateSalary('middle', 10)) // 200
```

### 2、解决if判断去获取值

```js
const type = 'gas'
const status = {
	co: '一氧化碳',
	watch: '手表',
    smartWatch: '手环',
    gas: '燃气'
}

if(status[type]){
    console.log(status[type])
}
```

### 3、解决多层if else 去判断调用方法

```js
function upgradecommd(){
    console.log('方法一')
}
function humanExistUpgrade(){
    console.log('方法二')
}
function methodThree(){
    console.log('方法三')
}

const deviceTypeStatus = {
    callhand: {
        '方法一': 'upgradecommd',
        '方法二': 'humanExistUpgrade',
    },
    humanExist: {
        '方法三': 'methodThree',
    }
}
const type = '方法一'
const value = deviceTypeStatus[type]
const keys = Object.keys(deviceTypeStatus)
if(keys.includes(value)){
    deviceTypeStatus[value] && [statusTitle[value]]();
}
```

