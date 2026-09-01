---
title: Vue中常见性能优化
date: 2022-02-17 12:03:22
tags: vue优化
comment: 'valine'
categories: 
- vue
---

# Vue中常见性能优化

### 编码优化:

1.不要将所有的数据都放在data中，data中的数据都会增加getter和setter，会收集对应的watcher；
2.vue 在 v-for 时给每项元素绑定事件需要用事件代理；
3.SPA 页面采用keep-alive缓存组件；
4.拆分组件( 提高复用性、增加代码的可维护性,减少不必要的渲染 )；
5.v-if 当值为false时内部指令不会执行,具有阻断功能，很多情况下使用v-if替代v-show；
6.key 保证唯一性 ( 默认 vue 会采用就地复用策略 )；
7.Object.freeze 冻结数据；
8.合理使用路由懒加载、异步组件；
9.尽量采用runtime运行时版本；
10.数据持久化的问题 （防抖、节流）。

### Vue 加载性能优化:

1.第三方模块按需导入 ( babel-plugin-component )；
2.滚动到可视区域动态加载 ( https://tangbc.github.io/vue-virtual-scroll-list )；
3.图片懒加载 (https://github.com/hilongjw/vue-lazyload.git)。

### 用户体验:

1.app-skeleton 骨架屏；
2.app-shell app壳 ；
3.pwa serviceworker。

### SEO 优化：

1.预渲染插件 prerender-spa-plugin；
2.务端渲染 ssr。

### 打包优化:

1.使用 cdn 的方式加载第三方模块；
2.多线程打包 happypack splitChunks 抽离公共文件；
3.sourceMap 生成。

### 缓存，压缩

1.客户端缓存、服务端缓存；
2.服务端 gzip 压缩
