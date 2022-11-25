---
title: Vue自定义指令
date: 2022-07-08 16:25:22
tags: 自定义指令
comment: 'valine'
categories: 
- vue
---
# Vue自定义指令

## 一、vue自定义指令API

### 1.钩子函数

- `bind`：只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置。
- `inserted`：被绑定元素插入父节点时调用 (仅保证父节点存在，但不一定已被插入文档中)。
- `update`：所在组件的 VNode 更新时调用，**但是可能发生在其子 VNode 更新之前**。指令的值可能发生了改变，也可能没有。但是你可以通过比较更新前后的值来忽略不必要的模板更新 (详细的钩子函数参数见下)。
- `componentUpdated`：指令所在组件的 VNode **及其子 VNode** 全部更新后调用。
- `unbind`：只调用一次，指令与元素解绑时调用。

### 2.钩子函数参数

指令钩子函数会被传入以下参数：

- `el`：指令所绑定的元素，可以用来直接操作 DOM。

-  binding对象，包含以下 property：

  - `name`：指令名，不包括 `v-` 前缀。
- `value`：指令的绑定值，例如：`v-my-directive="1 + 1"` 中，绑定值为 `2`。
  - `oldValue`：指令绑定的前一个值，仅在 `update` 和 `componentUpdated` 钩子中可用。无论值是否改变都可用。
  - `expression`：字符串形式的指令表达式。例如 `v-my-directive="1 + 1"` 中，表达式为 `"1 + 1"`。
  - `arg`：传给指令的参数，可选。例如 `v-my-directive:foo` 中，参数为 `"foo"`。
  - `modifiers`：一个包含修饰符的对象。例如：`v-my-directive.foo.bar` 中，修饰符对象为 `{ foo: true, bar: true }`。
  
- `vnode`：Vue 编译生成的虚拟节点。移步 [VNode API](https://cn.vuejs.org/v2/api/#VNode-接口) 来了解更多详情。

- `oldVnode`：上一个虚拟节点，仅在 `update` 和 `componentUpdated` 钩子中可用。





## 二、自定义指令例子 

### 1.聚焦指令（v-focus）

​		除了核心功能默认内置的指令 (`v-model` 和 `v-show`)，Vue 也允许注册自定义指令。注意，在 Vue2.0 中，代码复用和抽象的主要形式是组件。然而，有的情况下，你仍然需要对普通 DOM 元素进行底层操作，这时候就会用到自定义指令。举个聚焦输入框的例子

​		需求：当页面加载时，该元素将获得焦点，只要你在打开这个页面后还没点击过任何内容，这个输入框就应当还是处于聚焦状态。

现在让我们用指令来实现这个功能：

```js
// 注册一个全局自定义指令 `v-focus`
// 可以新建一个directive文件夹，里面放v-focus.js
Vue.directive('focus', {
  // 当被绑定的元素插入到 DOM 中时……
  inserted: function (el) {
    // 聚焦元素
    el.focus()
  }
})
```

如果想注册局部指令，组件中也接受一个 `directives` 的选项：

```js
//和method同级
directives: {
  focus: {
    // 指令的定义
    inserted: function (el) {
      el.focus()
    }
  }
}
```

然后你可以在模板中任何元素上使用新的 `v-focus` property，如下：

```html
<input v-focus>
```

### 2.图片懒加载指令（v-LazyLoad）

需求：实现一个图片懒加载指令，只加载浏览器可见区域的图片。

思路：

1. 图片懒加载的原理主要是判断当前图片是否到了可视区域这一核心逻辑实现的
2. 拿到所有的图片 Dom ，遍历每个图片判断当前图片是否到了可视区范围内
3. 如果到了就设置图片的 `src` 属性，否则显示默认图片

图片懒加载有两种方式可以实现，一是绑定 `srcoll` 事件进行监听，二是使用 `IntersectionObserver` 判断图片是否到了可视区域，但是有浏览器兼容性问题。

下面封装一个懒加载指令兼容两种方法，判断浏览器是否支持 `IntersectionObserver` API，如果支持就使用 `IntersectionObserver` 实现懒加载，否则则使用 `srcoll` 事件监听 + 节流的方法实现。

```js
const LazyLoad = {
  // install方法
  install(Vue, options) {
    const defaultSrc = options.default
    Vue.directive('lazy', {
      bind(el, binding) {
        LazyLoad.init(el, binding.value, defaultSrc)
      },
      inserted(el) {
        if (IntersectionObserver) {
          LazyLoad.observe(el)
        } else {
          LazyLoad.listenerScroll(el)
        }
      },
    })
  },
  // 初始化
  init(el, val, def) {
    el.setAttribute('data-src', val)
    el.setAttribute('src', def)
  },
  // 利用IntersectionObserver监听el
  observe(el) {
    var io = new IntersectionObserver((entries) => {
      const realSrc = el.dataset.src
      if (entries[0].isIntersecting) {
        if (realSrc) {
          el.src = realSrc
          el.removeAttribute('data-src')
        }
      }
    })
    io.observe(el)
  },
  // 监听scroll事件
  listenerScroll(el) {
    const handler = LazyLoad.throttle(LazyLoad.load, 300)
    LazyLoad.load(el)
    window.addEventListener('scroll', () => {
      handler(el)
    })
  },
  // 加载真实图片
  load(el) {
    const windowHeight = document.documentElement.clientHeight
    const elTop = el.getBoundingClientRect().top
    const elBtm = el.getBoundingClientRect().bottom
    const realSrc = el.dataset.src
    if (elTop - windowHeight < 0 && elBtm > 0) {
      if (realSrc) {
        el.src = realSrc
        el.removeAttribute('data-src')
      }
    }
  },
  // 节流
  throttle(fn, delay) {
    let timer
    let prevTime
    return function (...args) {
      const currTime = Date.now()
      const context = this
      if (!prevTime) prevTime = currTime
      clearTimeout(timer)

      if (currTime - prevTime > delay) {
        prevTime = currTime
        fn.apply(context, args)
        clearTimeout(timer)
        return
      }

      timer = setTimeout(function () {
        prevTime = Date.now()
        timer = null
        fn.apply(context, args)
      }, delay)
    }
  },
}

export default LazyLoad
```

使用，将组件内  标签的 `src` 换成 `v-LazyLoad`

```html
<img v-LazyLoad="xxx.jpg" />
```

### 3.权限验证（v-permission）

需求：自定义一个权限指令，对需要权限判断的 Dom 进行显示隐藏。

思路：

1. 自定义一个权限数组
2. 判断用户的权限是否在这个数组内，如果是则显示，否则则移除 Dom

```js
function checkArray(key) {
  let arr = ['admin', 'test', 'user', 'pro']
  let index = arr.indexOf(key)
  if (index > -1) {
    return true // 有权限
  } else {
    return false // 无权限
  }
}

const permission = {
  inserted: function (el, binding) {
    let permission = binding.value // 获取到 v-permission的值
    if (permission) {
      let hasPermission = checkArray(permission)
      if (!hasPermission) {
        // 没有权限 移除Dom元素
        el.parentNode && el.parentNode.removeChild(el)
      }
    }
  },
}

export default permission
```

使用：给 `v-permission` 赋值判断即可

```html
<div class="btns">
  <!-- 显示 -->
  <button v-permission="'admin'">新增</button>
  <!-- 不显示 -->
  <button v-permission="'person'">删除</button>
</div>
```