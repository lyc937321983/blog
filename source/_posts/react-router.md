---
title: react-router
date: 2024-08-23 10:38:22
tags: router
comment: 'valine'
categories: 
- react

---

# react-router

## 一、使用react-router

```jsx
import { createBrowserRouter, RouterProvider, Navigate } from 'react-router-dom'
import Page1 from './pages/page1'
import Page2 from './pages/page2'
import Page3 from './pages/page3'

const router = createBrowserRouter([{
  path: '/page1',
  Component: Page1,
}, {
  path: '/page2',
  Component: Page2,
}, {
  path: '/page3',
  Component: Page3,
}])


function App() {
  return (
    <RouterProvider router={router} />
  )
}

export default App
```

## 二、重定向

```jsx
{
  path: '/',
  element: <Navigate to="/page1" />,
}
```

## 三、自定义404页面

```jsx
import NotFound from './NotFound'
...
{
  path: '*',
  Component: NotFound,
}
```

## 四、Link组件实现路由跳转

```jsx
<div>
    <Link to='/page1'>page1</Link>
    <RouterProvider router=frouter} />
</div>
```

## 五、路由按需加载

​		借助react的`lazy`和`Suspense`可以轻松的实现按需加载。路由按需加载是优化首屏时间的一个重要方法，相当于把一个大的包，拆成了一个个小包，只有访问某个页面的时候，才去加载对应的js，减少了首屏js的体积。使用lazy动态导入组件

```jsx
const router = createBrowserRouter([{
	path:'/'，
	Component: Layout,
	children: [{
		path:'/page1'
		Component:lazy(()=import('./pages/page1/index.tsx'))
	}，
	{
		path:'/page2'，
		Component:lazy(()import('./pages/page2/index.tsx'))
	}，
	{
		path:'/page3'
		Component:lazy(()=import('./pages/page3/index.tsx')),
	}
	{
		path:'/',
		element:<Navigate to="/page1"/>
	}，
	{
		path:'*',
		Component: NotFound
	}
])
```

