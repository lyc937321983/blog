---
title: 前端规范总结(阿里)
date: 2024-08-23 10:25:22
tags: 前端规范
comment: 'valine'
categories: 
- js
---

# 前端规范总结(阿里)

## 一、HTML

1.规定字符编码 meta charset

2.IE 兼容模式 content="IE=Edge"

3.doctype大写

4.减少div，多使用header，footer等语义标签

5.属性使用双引号

## 二、CSS

- 类目小写用中划线 fw-test-0010
- 子类选择器不使用.xx选择器使用 > 选择器
- 使用缩写属性
- 属性单独占用一行
- 省略0后单位
- 减少使用ID选择器

## 三、LESS

- 公共文件放在style/less/common中
- 代码顺序 @import引用 @变量声明 .page样式说明
- 减少嵌套层级（<20行）

## 四、JavaScript

- 驼峰命名不能使用下划线
- 变量、函数、成员变量、局部变量都是用该规则
- 常量命名全部大写、间隔使用下划线、意思表达完整无需简写

## 五、代码格式

- tab缩进默认两格
- 字符串引号使用单引号
- 对象声明使用字面量 code a = [] Not a = new Object()
- 使用ES6+语法
- 关键词if....后必须有大括号
- undefined永远不能作为判断
- 循环判断不能超过三层
- 上下文的this只能使用self
- 慎用console.log会导致性能问题

## 六、Vue规范

- 组件名为多单词
- 组件文件明使用pascal-case格式
- 基础组件文件名使用base开头
- 和父组件紧密耦合的子组件应该以父组件名作为前缀命名
- data必须为函数
- Prop定义必须使用驼峰、指定类型、加上注释说明、加上required或defalut、必须加上validator验证
- 组件样式必须使用scoped
- 较多特性必须换行
- 组件模板必须使用简单表达式(模板元素内少嵌入计算代码，多使用计算属性computed)
- 指令使用缩写格式
- 标签顺序保持一致
- v-for必须设置key
- v-show(高频开关显示)与v-if(低频开关显示)选择
- script顺序(name > components > mixins > props > data > computed > watch > filter > 钩子函数 > methods)

## 七、Vue Router规范

- 页面跳转直接使用路由传参不使用Vuex
- 使用懒加载
- router命名 path、childrenPoints才用kebab-case命名，name使用组件命名规则
- path必须使用/开头

## 八、Vue项目结构规范

- 项目结构命名必须与后端统一
- 使用Vue-cli作为脚手架
- 文件结构规则

```sql
src	源码目录
 | - api    api接口
 | - aassets   静态资源
 | - components   公共组件
 | - config    配置信息
 | - constants   常量信息
 | - directives   自定义指令
 | - filters     过滤器，全局工具
 | - datas   模拟数据 临时文件
 | - lib    外部引用插件及修改文件
 | - mock   模拟接口 临时文件
 | - plugins   插件全局使用
 | - router   路由统一管理
 | - store  Vuex统一管理
 | - themes   自定义主题文件
 | - views    视图目录
 | - | - role    role模块名
 | - | - role-list   role列表页面
 | - | - role-add    role新建页面
 | - | - role-update   role更新页面
 | - | - index.less    role模块样式
 | - | - components    role模块通用组件文件夹
 | - employee    employee模块
```

### api目录

- 文件、变量要与后端接口一致
- 一个controller对应一个api文件
- api方法名字要与后端api url保持一致
- api每个方法都要添加注释 与后端swagger文档一致

### assets目录

- 主要放置images、styles、icons等，命名规范kebab-case

### components目录

- 目录及文件都是用kebab-case命名

### constants目录

- 存放所有常量，如需放在vue使用要安装vue-enum插件（枚举插件）

### router 与 store目录

- router与store必须拆分
- router按照views结构保持一致
- store按照业务拆分js

### views目录

- 命名与后端一致
- 组件命名用PascalCase规则

### 注释说明

- 公共组件说明、api接口处、store（state、mutation、action）处、vue template处、methods处、data非常见单词处必须加注释

## 十、其他

- 不要手动操作DOM
- 删除无用的代码console.log等