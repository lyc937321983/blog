---
title: rem适配PC端和移动端
date: 2025-01-09 9:30:22
tags: rem适配
comment: 'valine'
categories: 
- css
---

# rem适配PC端和移动端(三种方式)

### 一、媒体查询,根据屏幕尺寸,设置rem的值

假设目前需要适配移动端320,360,375,1920的分辨率,才用scss,设置一个变量$no为10,用于分10份,

- @media---开启媒体查询
- screen---查询的类型(屏幕宽度)
- max-width最大值
- min-width最小值

global.scss,在main.js中引入

```scss
$no: 10;
//表示宽度最大值为320的情况下,设置rem为 320/10 px,即32px
//所以只要小于320px,统一rem为32px,这样就设置了最小值
@media screen and (max-width: 320px) {
  html {
    font-size: 320px / $no
  }
}

//表示宽度最小值为320的情况下,设置rem为 320/10 px,即32px
//所以只要大于320,rem值就为32px
@media screen and (min-width: 320px) {
  html {
    font-size: 320px / $no
  }
}

//表示宽度最小值为360的情况下,设置rem为 360/10 px,即36px
//所以只要大于等于360,rem的值就为36px
@media screen and (min-width: 320px) {
  html {
    font-size: 320px / $no
  }
}

//下面也是同理,同时利用css下方样式的覆盖性,只要按顺序写就能保证能检测到
//表示宽度最小值为375的情况下,设置rem为 375/10 px,即37.5px
//所以只要大于等于375,rem的值就为37.5px
@media screen and (min-width: 375px) {
  html {
    font-size: 375px / $no
  }
}

//PC端适配--使用pc端的分辨率就行
@media screen and (min-width: 1920px) {
  html {
    font-size: 1920px / $no 
  }
}

//最后设置个最大值,max-width: 1920px--->最大值为1920px
//只要小于1920px,统一rem为192px,这样就设置了最大值
@media screen and (max-width: 1920px) {
  html {
    font-size: 1920px / $no 
  }
}
```

最后在网页中使用rem做单位,假如100px在375px中长度就用2.66rem做单位(有插件可以转换,下文介绍)

```css
 .box {
    width: 2.66rem;
    height: 2.66rem;
  }
```

### 二、媒体查询+flexible.js

flexible.js就是手淘团队推出转化rem的方法,复制引用就好,原理就是给每个html标签添加个行内样式 `style="font-size: xxx px "`

```js
 (function flexible (window, document) {
  var docEl = document.documentElement
  var dpr = window.devicePixelRatio || 1

  // adjust body font size
  function setBodyFontSize () {
    if (document.body) {
      document.body.style.fontSize = (12 * dpr) + 'px'
    }
    else {
      document.addEventListener('DOMContentLoaded', setBodyFontSize)
    }
  }
  setBodyFontSize();
   
   
  //这里也是设置的份数,也是用10
  // set 1rem = viewWidth / 10 
  // 把屏幕平均分成10等份。比如1920/10= 192 px,这个时候1rem就是192px,配合vscod插件快速适配，在style中使用媒体查询。
  function setRemUnit () {
    var rem = docEl.clientWidth / 10
    docEl.style.fontSize = rem + 'px'
  }

  setRemUnit()

  // reset rem unit on page resize
  window.addEventListener('resize', setRemUnit)
  window.addEventListener('pageshow', function (e) {
    if (e.persisted) {
      setRemUnit()
    }
  })

  // detect 0.5px supports
  if (dpr >= 2) {
    var fakeBody = document.createElement('body')
    var testElement = document.createElement('div')
    testElement.style.border = '.5px solid transparent'
    fakeBody.appendChild(testElement)
    docEl.appendChild(fakeBody)
    if (testElement.offsetHeight === 1) {
      docEl.classList.add('hairlines')
    }
    docEl.removeChild(fakeBody)
  }
}(window, document))
```

在main.js引入 `import '@/utils/flexible'`

设置最大值和最小值,加个important,提升权重

```scss
@media screen and (max-width: 320px) {
  html {
    font-size: 320px / $no!important
  }
}

@media screen and (max-width: 1920px) {
  html {
    font-size: 1920px / $no !important
  }
}
```

### 插件转化的问题

vscode安装cssrem插件

![Snipaste_2024-12-22_22-36-07.png](https://p6-xtjj-sign.byteimg.com/tos-cn-i-73owjymdk6/ac429fc15ae54cab98b58e98eda694f8~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg5Lmd562SNzk5:q75.awebp?rk3s=f64ab15b&x-expires=1736691325&x-signature=8bt3rYCiLQt2r9Zgkh73ezZN5NU%3D) 找到设置根字体大小的地方,假设设计稿为1920,设置为10等份,那根字体大小就为1920/10=192,之后写css的时候,只要写 xx px,即会有转化rem选项

![Snipaste_2024-12-22_22-36-42.png](https://p6-xtjj-sign.byteimg.com/tos-cn-i-73owjymdk6/cb9674ebbfba4b42bfa172628698a995~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg5Lmd562SNzk5:q75.awebp?rk3s=f64ab15b&x-expires=1736691325&x-signature=kiF9tnPU0qJ40Ryh0pYAkayUb4c%3D)

### 三、postcss-pxtorem插件自动转化

##### 安装

可能会有版本问题,遇到可以安装postcss-pxtorem@5.1.1

```shell
npm install postcss postcss-pxtorem --save-dev
```

##### 配置

在你的项目根目录中创建一个 `postcss.config.js` 文件，并添加以下配置：

```js
 //配置自行选择使用,rootValue和propList应该为必选项
module.exports = {
  plugins: {
    'postcss-pxtorem': {
      rootValue: 37.5, // 表示根元素字体大小或根据input参数返回根元素字体大小
      propList: ['*'], // 可以从px更改为rem的属性, 通配符*表示启用所有属性
      selectorBlackList: ['.norem'], // 过滤掉.norem开头的class，不进行rem转换
      replace: true, // 是否直接替换原来的 px 单位
      mediaQuery: false, // 是否转换媒体查询中的 px
      minPixelValue: 1// 设置要替换的最小像素值
    }
  }
}
```

##### 创建utils/rem.js,在main.js中引入,之后直接使用单位px,会自动转化

```js
 // 配置基本大小
let baseSize = 37.5;

// 设置 rem 函数
function setRem () {
  //当前页面宽度相对于375px屏幕宽的缩放比例，可根据自己需要修改。
  let scale = document.documentElement.clientWidth / 375;
  //设置页面根节点字体大小（“Math.min(scale, 2)” 指最高放大比例为2，即最大根字体大小为37.5*2=75，可根据实际业务需求调整）
  let fz = baseSize * Math.min(scale, 2)
  //获得根字体大小后,给全部页面设置根字体大小
  //业务需要限制显示页面的最小值,我这里是根据倍数限制的,因为1倍的字体大小为37.5,具体情况可以自己设定
  document.documentElement.style.fontSize = scale >= 1 ? fz + 'px' : 37.5 + 'px'

}
setRem(); //初始化

// 适配 - 重置函数
function resetRem (num) {
  if (num) baseSize = Number(num);
  setRem();
}
window.resetRem = resetRem; // 全局可调用(其他方式也可)

// 改变窗口大小时重置 rem
window.onresize = function () {
  setRem()
};
```

三种方式都可以实现适配,个人感受如下:

- 第一种比较原生,每多适配一个就得多写一个媒体查询
- 第二种属于第一种的进阶版
- 第三种为插件转化,更方便,但可能会有版本兼容的问题