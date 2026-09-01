---
title: Vue混入mixin
date: 2022-07-08 17:25:22
tags: mixin
comment: 'valine'
categories: 
- vue
---

# Vue混入mixin

## 1、什么是混入

**Vue官方：**

> 混入 (mixin) 提供了一种非常灵活的方式，来分发 Vue 组件中的可复用功能。一个混入对象可以包含任意组件选项。当组件使用混入对象时，所有混入对象的选项将被“混合”进入该组件本身的选项。

**自悟：**

> 将组件的公共逻辑或者配置提取出来，哪个组件需要用到时，直接将提取的这部分混入到组件内部即可。这样既可以减少代码冗余度，也可以让后期维护起来更加容易

**混入分为`全局混入`和`局部混入`**

> 局部混入和组件的按需加载有点类似，就是需要用到mixin中的代码时，我们再在组件章引入它。
全局混入的话，则代表我在项目的任何组件中都可以使用mixin。

## 2、实例

### 1.准备工作

在我们的项目src目录下新建mixin文件夹，然后新建index.js文件，该文件存放我们的mixin代码。

**代码如下：**

```js
// src/mixin/index.js
//可以看到我们的mixin非常的简单，主要包含了一个Vue组件的常见的逻辑结构
export const mixins = {
  data() {
    return {
      msg: "我是小猪课堂",
    };
  },
  computed: {},
  created() {
    console.log("我是mixin中的created生命周期函数");
  },
  mounted() {
    console.log("我是mixin中的mounted生命周期函数");
  },
  methods: {
    clickMe() {
      console.log("我是mixin中的点击事件");
    },
  },
};
```

### 2.局部混入

组件中引入mixin也非常简单，修改App.vue组件。

**代码如下：**

```vue
// src/App.vue
<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png" />
    <button @click="clickMe">点击我</button>
  </div>
</template>

<script>
import { mixins } from "./mixin/index";
export default {
  name: "App",
  mixins: [mixins],
  components: {},
  created(){
    console.log("组件调用minxi数据",this.msg);
  },
  mounted(){
    console.log("我是组件的mounted生命周期函数")
  }
};
</script>
```

### 3.全局混入

上一点我们使用mixin是在需要的组件中引入它，我们也可以在全局先把它注册好，这样我们就可以在任何组件中直接使用了。

修改main.js，代码如下：

```js
import Vue from "vue";
import App from "./App.vue";
import { mixins } from "./mixin/index";
Vue.mixin(mixins);

Vue.config.productionTip = false;

new Vue({
  render: (h) => h(App),
}).$mount("#app");
```

然后把App.vue中引入mixin的代码注释掉，代码如下：

```vue
<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png" />
    <button @click="clickMe">点击我</button>
    <button @click="changeMsg">更改mixin数据</button>
    <demo></demo>
  </div>
</template>

<script>
// import { mixins } from "./mixin/index";
import demo from "./components/demo.vue";
export default {
  name: "App",
  // mixins: [mixins],
  components: { demo },
  created() {
    console.log("组件调用minxi数据", this.msg);
  },
  mounted() {
    console.log("我是组件的mounted生命周期函数");
  },
  methods: {
    changeMsg() {
      this.msg = "我是变异的小猪课堂";
      console.log("更改后的msg:", this.msg);
    },
  },
};
</script>
```

虽然这样做很方便，但是我们不推荐，来看看官方的一段话：

> 请谨慎使用全局混入，因为它会影响每个单独创建的 Vue 实例 (包括第三方组件)。大多数情况下，只应当应用于自定义选项，就像上面示例一样。推荐将其作为[插件](https://link.juejin.cn/?target=https%3A%2F%2Flink.zhihu.com%2F%3Ftarget%3Dhttps%3A%2F%2Fcn.vuejs.org%2Fv2%2Fguide%2Fplugins.html)发布，以避免重复应用混入。