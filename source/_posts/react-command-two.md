---
title: react常用的两个Hook
date: 2022-03-16 15:12:22
tags: react常用的两个Hook
comment: 'valine'
categories: 
- react
---

# React最常用的两个Hook

## 一.useState

代码中：

React.useState(0)相当于class组件中的this.state添加一个属性值

variable相当于class组件中的this.state. variable的值

setVariable可以用来修改variable的值，可以相当于class组件中的this.setState

```jsx
import React,(useState) from 'react'
export default function useState_Demo() {
    const [variable, setVariable] = useState(0);//通过解构赋值，我们拿到的variable、setVariable
    function changeVariable(){
        setVariable((variable) => variable +1) //setVariable的回调中传进来的参数是variable
    }
    render (
        <div> 
            <button onclick = {change}>点我会使variable+1</button>
        </div>
    )
}
```

## 二.useEffect

代码中：

以下代码中可以看出，useEffect的使用代替了在class组件中componentDidMoun、componentDidUpdate、componentWillUnmount的使用

```jsx
import React,(useState, useEffect) from 'react'
export default function useState_Demo() {
   const [variable, setVariable] = useState(0);//通过解构赋值，我们拿到的variable、setVariable
    
    useEffect(() => {
        //这个return是在该组件监测的数据变化时或者被卸载时调用的，被卸载时调用可以相当于componentWillUnmount钩子 
        return () => {
            console.log('该组件被卸载了')
        }
    }, [variable])//第二个参数传了[variable]，表示检测variable的更新变化，一旦variable变化就会再次执行useEffect的回调
                  //第二个参数传了[],表示谁都不检测只执行一次useEffect的回调，相当于componentDidMount钩子
                  //第二个参数不传参，只要该组件有state变化就会执行useEffect的回调，相当于componentDidUpdate钩子
    function changeVariable(){
        setVariable((variable) => variable +1) //setVariable的回调中传进来的参数是variable
    }
    render (
        <div> 
            <button onclick = {change}>点我会使variable+1</button>
        </div>
    )
}
```

## 三.使用hook需要注意的

1、只在组件函数的最外层使用Hook，不要在循环，条件或嵌套函数中调用 Hook

```jsx
import React,(useEffect) from 'react'
export default function useState_Demo() {
   //这里才是正确的
   useEffect(() => {})
    
   //错误1，使用了条件判断
   if(true) {
        useEffect(() => {})
   }
   //错误2，使用了循环
   while(true) {
        useEffect(() => {})
   }
  //错误3,使用了嵌套
  useEffect(() => {
      useEffect(() => {})
  })
}
```

2、不能在组件的函数外使用Hook

```jsx
import React,(useState, useEffect) from 'react'
//错误1，在组件函数外使用了useState
const [variable, setVariable] = useState(0);
//错误2，在组件函数外使用了useEffect
useEffect(() => {})
export default function useState_Demo() {
   //在组件函数里使用才是正确的
   const [variable, setVariable] = useState(0);
}
```

3、为了避免以上的错误，可以 安装[`eslint-plugin-react-hooks`](https://link.juejin.cn/?target=https%3A%2F%2Fwww.npmjs.com%2Fpackage%2Feslint-plugin-react-hooks) ESLint 插件来检查代码上错误

## 四.自定义hook

hook就是一个函数，自定义hook是为了方便组件之间共享逻辑，其实就是对复用功能进行封装，自定义hook内部也调用了react自带的hook，命名要以use开头

```jsx
//自定义hook
function useHook(initState) {
  const [variable, setVariable] = useState(initState)
  return variable;
}
//使用自定义hook
export default function useState_Demo() {
    const variableState = useHook(0)
}
```

可能你会疑惑，多个组件中使用相同的 Hook 会共享 state 吗？

答案是：不会。因为每次调用react自带的hook都是独自互不影响的，所以自定义hook被多次重复调用也是独自互不影响的