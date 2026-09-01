---
title: vue 指令的本质是什么?
date: 2022-02-16 15:55:22
tags: vue 指令
comment: 'valine'
categories: 
- vue
---


# vue 指令的本质是什么？

### 1.用过哪些 vue 内置指令？

**v-text**

相当于原生 js 中的 innerText。

```vue
<p v-text="msg1">hello world</p>
<p>{{ msg2 }}</p>
data() {
  return {
    msg: 'hello lin',
    msg2: 'hell paul'
  }
}
```

**v-html**

相当于原生 js 中的 innerHtml。

```vue
<div v-html="htmlStr"></div>

data() {
  return {
    htmlStr: '<div style="color: red">hello world </div>',
  }
}
```

**v-pre**

```vue
<span v-pre>{{ msg }}</span>
```

因为 vue 的插值语法为`{{ }}`，所以 vue 提供了 v-pre 这个指令来显示原始 Mustache 标签。

**v-once**

只渲染元素和组件**一次**。随后的重新渲染，元素/组件及其所有的子节点将被视为静态内容并跳过。这可以用于优化更新性能。

```vue
<!-- 单个元素 -->
<span v-once>This will never change: {{msg}}</span>
<!-- 有子元素 -->
<div v-once>
  <h1>comment</h1>
  <p>{{msg}}</p>
</div>
<!-- 组件 -->
<my-component v-once :comment="msg"></my-component>
<!-- `v-for` 指令-->
<ul>
  <li v-for="i in list" v-once>{{i}}</li>
</ul>
```

**v-cloak**

基本用不到，因为在单文件的开发模式下这个指令没用，在 html 里直接写模版的时候才需要用到，就不介绍了。

### 2.vue 指令的本质是什么？

> 指令的本质是语法糖，或者说标志位。

其实，vue 指令并不是很高大上的东西，它只是语法糖，或者说是标志位，是用来帮助我们简化代码的。

在 vue 的编译阶段，从 template 编译转化到 render 函数的过程中，会把这些指令编译成对应的 js 代码，实现对应的功能。

这也是为什么 jsx 不支持 vue 指令，需要配置 babel 插件`@vue/babel-preset-jsx` 和 `@vue/babel-helper-vue-jsx-merge-props` 才行，因为 jsx 本身也是语法糖，也是会被编译成 render 函数的。

### 3.如何实现 vue 自定义指令？

Vue 允许注册自定义指令，有全局注册和局部注册两种方式。

**注册全局指令**

```vue
Vue.directive('focus', {
  inserted: function (el) {
    el.focus()
  }
})
```

**注册局部指令**

组件中接受一个 `directives` 的选项：

```vue
directives: {
  focus: {
    inserted: function (el) {
      el.focus()
    }
  }
}
```

然后你可以在模板中任何元素上使用新的 `v-focus` property，如下：

```vue
<input v-focus />
```

一个指令定义对象可以提供如下几个钩子函数 (均为可选)：

- bind: 只调用一次，指令第一次绑定到元素时调用，可以定义一个在绑定时执行一次的初始化动作，此时获取父节点为null。
- inserted: 被绑定元素插入父节点时调用（仅保证父节点存在，但不一定已被插入文档中），此时可以获取到父节点。
- update: 所在组件的 VNode 更新时调用，**但是可能发生在其子 VNode 更新之前**。指令的值可能发生了改变，也可能没有。但是你可以通过比较更新前后的值来忽略不必要的模板更新
- componentUpdated: 指令所在组件的 VNode **及其子 VNode** 全部更新后调用。
- unbind: 只调用一次， 指令与元素解绑时调用。

我们通过下面这个例子来感受下这几个钩子函数的调用时机：

```vue
<button v-if="showBtn" v-append-text="`hello ${number}`" @click="number++">按钮</button>
<button @click="showBtn = !showBtn">销毁</button>

data() {
  return {
     showBtn: true,
     number: 1
  }
}
directives: {
  appendText: {
    bind() {
      console.log('bind :>> ')
    },
    inserted(el, binding) {
      el.appendChild(document.createTextNode(binding.value))
      console.log('inserted :>> ', el, binding)
    },
    update() {
      console.log('update :>> ')
    },
    componentUpdated(el, binding) {
      el.removeChild(el.childNodes[el.childNodes.length - 1])
      el.appendChild(document.createTextNode(binding.value))
      console.log('componentUpdated :>> ')
    },
    unbind() {
      console.log('unbind :>> ')
    }
  }
}
```

这个指令 `v-append-text` 实现的功能是，向元素中插入 textNode。

### 4.用过哪些自定义指令？

汇总一下我看到过或用过的比较好用的指令。

#### [cm-directives](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2FMichael-lzg%2Fv-directives)

- 复制粘贴指令 v-copy
- 长按指令 v-longpress
- 输入框防抖指令 v-debounce
- 禁止表情及特殊字符 v-emoji

- 权限校验指令 v-premission
- 实现页面水印 v-waterMarker
- 拖拽指令 v-draggable

这个库包含上面七个指令，可以选择 npm 安装 [cm-directives](https://link.juejin.cn/?target=https%3A%2F%2Fwww.npmjs.com%2Fpackage%2Fcm-directives)使用。

更建议 copy 一下这个库的指令代码到我们自己的项目，毕竟很多时候会改造一下，比如v-premission，一般要和自家的后端商量约定一下，其他指令 copy 下来可以直接用，这个库的 [README.md](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2FMichael-lzg%2Fv-directives) 写得非常详细。

#### [v-loading](https://link.juejin.cn/?target=https%3A%2F%2Felement.eleme.cn%2F%23%2Fzh-CN%2Fcomponent%2Floading) 加载

[element-ui](https://link.juejin.cn/?target=https%3A%2F%2Felement.eleme.cn%2F%23%2Fzh-CN%2Fcomponent%2Floading) 自带的指令，支持全屏和局部加载。

#### [v-track](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fl-hammer%2Fv-track) 埋点解决方案

v-track通过 Vue 自定义指令的方式将埋点代码与业务代码完全解耦~

#### [v-stickto](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fwormsan%2Fvue-stickto) 一个支持多DOM元素自动吸顶的vue指令

#### [v-lazyload](https://link.juejin.cn/?target=https%3A%2F%2Fwww.cnblogs.com%2Flzq035%2Fp%2F14183553.html) 懒加载图片

其实 img 标签是支持 lazy 属性的，就是兼容性不太好，所以一般懒加载还是要用支持懒加载的图片组件（比如[element-ui](https://link.juejin.cn/?target=https%3A%2F%2Felement.eleme.cn%2F%23%2Fzh-CN%2Fcomponent%2Fimage)），或者写个指令来实现。

可以参考下这篇文章的 v-lazyload

这里还有别人总结的更多的指令，用起来会大大提高开发效率。 

## 总结

vue 指令的本质是 语法糖，帮助我们简化代码和提高开发效率。