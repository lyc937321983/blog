---
title: JavaScript前端面试题
date: 2023-10-18 17:55:22
tags: 面试题
comment: 'valine'
categories: 
- interview
---
# JavaScript面试题

#### 1.简述同步和异步的区别

​		同步：按照一定的顺序去执行，执行完一个才能执行下一个
​		异步：执行顺序是不确定的，由触发条件决定，什么时间执行也是不确定的，即使是定时器，异步处理可以同时执行多个。

#### 2.怎么添加、移除、复制、创建、和查找节点

```scss
document.createElement
appendChild
removeChild
replaceChild
insertBefore
insertAfter
cloneNode
document.getElementById
```

#### 3.实现一个函数clone() 可以对Javascript中的五种主要数据类型（Number、string、Object、Array、Boolean）进行复制

```js
Object.prototype.clone = function(){
    var o = this.constructor === Array ? [] : {};
    for(var e in this){
        o[e] = typeof this[e] === "object" ? this[e].clone() : this[e];
    }
    return o;
}
```

#### 4.js如何消除一个数组里面重复的元素

​		方法一： 两层for循环遍历
​		方法二： 准备一个新空数组，将需要去重的数组进行遍历，判断新数组中是否有当前元素，若没有，这push到新数组中
​		方法三：利用ES标准中的新类型Set

```js
function unique(arr) {
    var set = new Set(arr);
    return Array.from(set);
}
```

#### 5.js写一个返回闭包的函数

```js
function fun1() {
     var sum = 0;
     function fun2() {
         sum++;
         return sum;
     }
     return fun2;
}
var s= fun1();
console.log(s());
```

#### 6.js使用递归完成1到100的累加

```js
function sum(num) {
    if(num==1){
        return 1;
    }
    return num+sum(num-1);
}
```

#### 7.Javascript有哪几种数据类型

​		String、Number、Boolean、空（Null）、未定义（Undefined）、Object、Symbol、bigInt

#### 8.js如何判断数据类型

​		typeof：原始类型，undfined和 function 数据类型
​		instanceof：区分自定义对象类型
​		Object.prototype.toString.call()：无法区分自定义对象类型

```js
function getType(obj) {  
  const type = typeof obj;  
  if (type === 'object') {  
    if (obj === null) return 'null';  
    if (Array.isArray(obj)) return 'array';  
    return Object.prototype.toString.call(obj).slice(8, -1);  
  }  
  return type;  
} 
```

#### 9.console.log(1+'2')和console.log(1-'2')的打印结果

```scss
12  -1
```

#### 10.Js的事件委托是什么，原理是什么

​		事件委托也称为事件代理。就是利用事件冒泡，把子元素的事件都绑定到父元素上。
何为事件冒泡呢？就是事件从最深的节点开始，然后逐步向上传播事件，举个例子：页面上有这么一个节点树，div>ul>li>a;比如给最里面的a加一个click点击事件，那么这个事件就会一层一层的往外执行，执行顺序a>li>ul>div，有这样一个机制，那么我们给最外面的div加点击事件，那么里面的ul，li，a做点击事件的时候，都会冒泡到最外层的div上，所以都会触发，这就是事件委托，委托它们父级代为执行事件

#### 11.如何改变函数内部的this指针的指向

​		call和apply会调用函数，并且改变函数内部this指向
​		call和apply传递的参数不一样，call传递参数arg1，arg2…形式，apply必须数组形式[arg]
​		bind不会调用函数，可以改变函数内部this指向

#### 12.列举几种解决跨域问题的方式，且说明原理

​		nginx反向代理
​		Node中间件代理
​		window.name + iframe
​		WebSocket协议跨域、PostMessage
​		jsonp

#### 13.谈谈垃圾回收机制的方式及内存管理

​	垃圾收集器会定期（周期性）找出那些不在继续使用的变量，然后释放其内存
​		2）标记清除算法：最常用的垃圾回收方式就是标记清除
​		3）引用计数算法：跟踪记录每个值被引用的次数

#### 14.写一个function ，清除字符串前后的空格

```js
function trim1(str){
    return str.replace(/(^\s*)|(\s*$)/g,"");
}
```

#### 15.js实现继承的方法有哪些

​		原型链继承
​		借用构造函数继承
​		组合继承（经典继承）
​		原型式继承
​		寄生式继承
​		寄生组合式继承
​		ES6、Class实现继承

#### 16.判断一个变量是否是数组，有哪些办法

```scss
Array.isArray(arr)
instanceof 运算符
Object.prototype.toString.call()
```

#### 17.let ，const ，var 有什么区别

​		1.变量声明和提升：var声明的变量存在变量提升，即变量可以在声明之前调用，值为undefined。然而，let和const声明的变量在其声明之前是不可用的，否则会报ReferenceError错误。这一点在ES6（ES2015）中是严格遵守的。
​		2.重复声明：var允许重复声明变量，后一个变量会覆盖前一个变量。然而，let和const在同一作用域内不允许重复声明变量，否则会报错。
​		3.块级作用域：var不存在块级作用域。然而，let和const存在块级作用域。ES5中作用域有全局作用域和函数作用域，但没有块作用域的概念。
​		4.赋值限制：const声明的是常量，必须赋值，且声明后不能再修改。var声明的变量没有这个限制，可以声明后不赋值，也可以声明后再赋值。但是var声明的变量如果不使用则会被浏览器优化而导致无法达到预期效果。

#### 18.箭头函数与普通函数有什么区别

​		1.外形不同：箭头函数使用箭头定义，普通函数中没有。
​		2.箭头函数全都是匿名函数：普通函数可以有匿名函数，也可以有具名函数
​		3.箭头函数不能用于构造函数：普通函数可以用于构造函数，以此创建对象实例。
​		4.箭头函数中 this 的指向不同：在普通函数中，this 总是指向调用它的对象，如果用作构造函数，它指向创建的对象实例。
​		5.箭头函数不具有 arguments 对象：每一个普通函数调用后都具有一个arguments 对象，用来存储实际传递的参数。但是箭头函数并没有此对象。
​		6.箭头函数不具有 prototype 原型对象。
​		7.箭头函数不具有 super

#### 19.随机取1-10之间的整数

```js
var randNum = Math.floor(Math.random() * 10) + 1;
console.log(randNum)
```

#### 20.new操作符具体干了什么

​	new 运算符创建一个用户定义的对象类型的实例或具有构造函数的内置对象的实例。new 关键字会进行如下的操作：
​    	1.创建一个新的空对象。
​    	2.将这个新对象的prototype属性链接到构造函数的prototype对象。
​    	3.将构造函数的this关键字绑定到新创建的对象，并执行构造函数的代码（添加属性和方法到新对象）。
​    	4.如果构造函数返回一个对象，那么这个对象将会替代构造函数中的this。如果构造函数没有返回一个对象，那么new操作符将返回this，即新创建的对象

#### 21.Ajax原理

​		通过javascript的方式，将前台数据通过xmlhttp对象传递到后台，后台在接收到请求后，将需要的结果，再传回到前台，这样就可以	实现不需要页面的回发，页是数据实现来回传递，从页实现无刷新
​		通过XmlHttpRequest对象来向服务器发异步请求，从服务器获得数据，然后用javascript来操作DOM而更新页面

#### 22.模块化开发怎么做

​		1.代码可读可维护
​		2.功能模块分离
​		3.使用模块化开发工具
​		4.ES6模块化规范(模块的导出和导入)
​		5.组件化开发:将UI组件进行封装和复用，采用组件化的方式进行开发，可以减少代码的冗余和提高开发效率。
​		6.前端工程化：通过前端工程化的思想，将前端项目拆分为多个子项目，每个子项目都有自己的作用域和功能，按需加载和执行，能够提高开发效率和用户体验。

#### 23.异步加载Js的方式有哪些

​		1.使用async属性：当脚本的async属性被设置为true时，浏览器会异步加载和执行脚本。这种方式不会阻塞页面的其他渲染，但可能会在脚本执行前或后加载页面内容。
​		2.使用defer属性：当脚本的defer属性被设置为true时，浏览器会延迟加载和执行脚本。这种方式会阻塞页面的渲染，直到脚本加载和执行完毕，但它通常在DOMContentLoaded事件触发前不会加载脚本。
​		3.使用onload事件：当文档加载完成后，可以通过监听window.onload或document.addEventListener("load", callback)等事件来异步加载JS。
​		4.使用DOMContentLoaded事件：当文档解析完成，DOMContentLoaded事件触发时，可以使用该事件来异步加载JS。
​		5.使用异步API：JavaScript还提供了许多异步API，如XMLHttpRequest和Fetch API等，可以使用这些API来异步加载JS。

#### 24.xml和 json的区别

​		JSON是JavaScript Object Notation；XML是可扩展标记语言。
​		JSON是基于JavaScript语言；XML源自SGML。
​		JSON是一种表示对象的方式；XML是一种标记语言，使用标记结构来表示数据项。
​		JSON不提供对命名空间的任何支持；XML支持名称空间。
​		JSON支持数组；XML不支持数组。
​		XML的文件相对难以阅读和解释；与XML相比，JSON的文件非常易于阅读。
​		JSON不使用结束标记；XML有开始和结束标签。
​		JSON的安全性较低；XML比JSON更安全。
​		JSON不支持注释；XML支持注释。
​		JSON仅支持UTF-8编码；XML支持各种编码

#### 25.webpack如何实现打包的

```scss
1.读取配置文件：Webpack首先会读取项目中的webpack.config.js配置文件。这个文件包含了打包所需要的所有信息，包括入口文件、输出路径、加载器（loaders）、插件（plugins）等等。
2.处理入口文件：Webpack会从配置文件中指定的入口文件开始，解析文件并记录其依赖。它会递归地解析这些依赖，并构建一个依赖图（dependency graph）。
3.确定输出路径：在配置文件中，你需要指定输出路径，Webpack会将最终打包后的文件保存到这个路径下。
4.加载器和插件：加载器可以将非JavaScript文件（如CSS、图片等）转换为Webpack可以处理的模块。插件则可以进行更广泛的任务，包括优化打包的输出、处理环境变量等等。
5.打包输出：最后，Webpack会将所有的模块打包成一个或多个文件，通常是一个JavaScript文件和一个CSS文件。它还会将这个文件名和路径写入到配置文件中的output字段。
```

#### 26.常见web安全及防护原理

```scss
SQL注入：具体就是通过把sql命令插入到web表单递交或输入域名或页面请求的查询字符串，最终达到欺骗服务器执行恶意sql命令
xss跨站脚本（Cross-site Scripting）：是一种网站应用程序的安全漏洞攻击，是代码注入的一种。它允许恶意用户将代码注入到网页上，其他用户在观看网页时就会受到影响。这类攻击通常包含了HTML以及用户端脚本语言。
CSRF/跨站请求伪造（Cross-site request forgery）：CSRF是一种夹持用户在已经登陆的web应用程序上执行非本意的操作的攻击方式
DDos攻击/分布式拒绝服务(Distributed Denial of service Attack)：攻击者想办法让目标服务器的磁盘空间、内存、进程、网络带宽等资源被占满，从而导致正常用户无法访问
上传漏洞：文件上传漏洞是指网络攻击者上传了一个可执行的文件到服务器，服务器未经任何检验或过滤，从而造成文件的执行。这里上传的文件可以是木马，病毒，恶意脚本或者WebShell等
目录遍历漏洞：由于Web服务器或者Web应用程序对用户输入的文件名称的安全性验证不足而导致的一种安全漏洞，使得攻击者通过利用一些特殊字符就可以绕过服务器的安全限制，访问任意的文件，甚至执行系统命令
```

#### 27.用过哪些设计模式

​		工厂方法模式、抽象工厂模式、单例模式、建造者模式、原型模式
​		适配器模式、装饰器模式、代理模式、外观模式
​		观察者模式、访问者模式、中介者模式、策略模式

#### 28.为什么要同源限制

​		限制不同源的document或脚本之间的相互访问，以免造成干扰和混乱

#### 29.offsetWidth/offsetHeight,clientWidth/clientHeight与scrollWidth/scrollHeight的区别

```scss
offsetWidth / offsetHeight（内容可视区域的高度 + 滚动条 + 边框+外边距）返回值包含content + padding + border，效果与 e.getBoundingClientRect()相同
clientWidth / clientHeight（内容可视区域的高度） 返回值只包含content + padding，如果有滚动条，也不包含滚动条
scrollWidth / scrollHeight（元素的padding+元素内容的高度） 返回值包含content + padding + 溢出内容的尺⼨
```

#### 30.js有哪些方法定义对象

```scss
通过构造函数来创建对象
通过var obj = new Object() 创建对象
通过var object = {}  对象字面量
```

#### 31.说说你对promise的了解

​	Promise 是异步编程的一种解决方案，将异步操作以同步操作的流程表达出来，避免了回调地狱的问题。
​	Promise 是一个构造函数，我们可以通过该构造函数来生成Promise的实例。
​	Promise对象有以下两个特点：

　　　　（1）对象的状态不受外界影响。Promise 即承诺，后续必要兑现，一旦兑现则不可更改！其有三种状态：pending（进行中）、fulfilled（已成功）和rejected（已失败）。
　　　　（2）一旦状态改变，就不会再变，任何时候都可以得到这个结果。Promise对象只有两种状态改变：从pending变为fulfilled和从pending变为rejected。只要这两种情况发生，状态就不会再变了。
　　Promise也有一些缺点：
　　　　首先，无法取消Promise，一旦新建它就会立即执行，无法中途取消。
　　　　其次，如果不设置回调函数，Promise内部抛出的错误，不会反应到外部。
　　　　第三，当处于pending状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。
　　Promise 的实例可以看做是一个状态展示器，我们可以将拥有状态及改变状态的业务通过Promise来实现，然后再结合async function进一步提升程序的可读性及易维护性。

#### 32.谈谈你对AMD、CMD的理解

```scss
AMD（Asynchronous Module Definition）：异步模块定义，AMD推崇依赖前置，在定义模块的时候就要声明其依赖的模块
CMD（Common Module Definition）：通用模块定义，CMD推崇就近依赖，只有在用到某个模块的时候再去移入。
CMD是sea.js推广过程中对模块定义的规范化产出；AMD是require.js推广过程中对模块定义的规范化产出
AMD和CMD都是异步加载模块，都是为了JavaScript模块化开发的规范
```

#### 33.web开发中会话跟踪的方法有哪些

```scss
URL重写：
隐藏表单域：
Cookie：
session：
```

#### 34.介绍js有哪些内置对象？

```scss
Math对象、Object对象、RegExp对象、 Global对象、Function对象、arguments、String对象、Number对象
Boolean对象、Date对象、Array对象
```

#### 35.说几条写JavaScript的基本规范？

```scss
在函数中，应该将变量声明提升到最上方，并且避免对应该变量使用多次var声明，在定义变量时，应该给变量提供一个初始化的值，以便于代码具有更好的阅读性。
代码中出现地址，时间等字符串应该使用常量代替。
在代码中比较时，应该使用 ===, !== 来代替 == 和 !=。
尽量不要在内置对象Array,data的原型对象上添加属性。
switch必须带有default分支。
for循环必须带有大括号，即使只有一行代码。
if语句必须使用大括号括起来
命名规范：驼峰命名法，常量必须全部大写
```

#### 36.javascript创建对象的几种方式？

```scss
使用{}创建对象，等同于 new Object()
通过new Object()创建对象
使用字面量创建对象
使用工厂模式创建对象
通过构造函数创建对象
通过原型模式创建对象
```

#### 37.eval是做什么的？

    在字符串里面写代码的函数

#### 38.null，undefined 的区别？

    一个是空，一个是未定义

#### 39.[“1”, “2”, “3”].map(parseInt) 答案是多少？

```js
//[1,NaN,NaN]
```

#### 40.javascript 代码中的”use strict”;是什么意思 ? 使用它区别是什么？

```scss
严格模式
禁止使用 with 语句，禁止 this 关键字指向全局对象，对象不能有重名的属性
```

#### 41.js延迟加载的方式有哪些？

```scss
defer属性
async属性
动态创建DOM
使用jq的getScript方法
使用setTimeout延迟加载脚本
将script引入放在最后（底部）加载
```

#### 42.defer和async

```scss
<script async src="xx.js"></script>
	async：加载和渲染后续文档元素的过程将和 xx.js 的加载与执行并行进行（异步）
<script defer src="xx.js"></script>
	defer，加载后续文档元素的过程将和xx.js的加载并行进行（异步），但是 xx.js的执行要在所有元素解析完成之后，
```

#### 43.说说严格模式的限制

```scss
变量必须声明后再使用
函数的参数不能有同名属性，否则报错
不能使用with语句
禁止this指向全局对象
不能对只读属性赋值，否则报错
```

#### 44.attribute和property的区别是什么？

```scss
Attribute是DOM节点自带属性
roperty是这个DOM元素作为对象
Attribute和roperty取值和赋值不同
```

#### 45.ECMAScript6 怎么写class么，为什么会出现class这种东西?

```scss
ES6的class可以看作只是一个语法糖
新的class写法只是让对象原型的写法更加清晰、更像面向对象编程的语法
```

#### 46.常见兼容性问题

```scss
阻止浏览器的默认行为的兼容写法 e.returnValue = false;//IE浏览器
阻止事件冒泡的兼容写法 e.cancelBubble = true;      //IE
事件监听绑定事件的兼容写法 ele.attachEvent("on"+myevent,cb)//IE
获取样式的兼容写法 ele.currentStyle[attr]
```

#### 47.函数防抖节流的原理

```scss
节流是第一个说了算，后续都会被节流阀屏蔽掉，防抖是最后一个说了算，前面启用的都会被清除

节流函数会在第一次触发事件时立即执行
防抖就是指触发事件后在 n 秒内函数只能执行一次
```

#### 48.原始类型有哪几种？null是对象吗？

```scss
undefined、null、string、number、boolean、symbol、
null其实并不是一个对象，尽管typeof null输出的是object,但是这其实是一个bug。在js最初的版本中使用的是32位系统，为了性能考虑地位存储变量的类型信息，000开头表示为对象类型，然而null为全0，故而null被判断为对象类型
```

#### 49.为什么console.log(0.2 + 0.1 == 0.3) //false

```scss
是由于浮点数精度问题造成的
IEEE754的64位双精度的语言都是如此
```

#### 50.说一下JS中类型转换的规则？

```scss
自动类型转换：JavaScript会自动转换值到需要的类型，例如，当一个值被赋值给一个不同类型的变量，或者当一个操作符需要一个特定类型的操作数时。
隐式类型转换：这种类型转换在计算表达式或者赋值操作时自动发生。例如，当一个字符串和一个数字相加时，字符串会被转换为数字然后进行加法运算。
显式类型转换：这涉及到使用一些函数或者方法来转换值的类型。例如，使用parseFloat()或者parseInt()函数将字符串转换为数字。
```

#### 51.深拷贝和浅拷贝的区别？如何实现

```scss
浅拷贝：是拷贝一层，属性为对象时，浅拷贝是复制，两个对象指向同一 个地址
	直接变量赋值
    Object.assign()；但目标对象只有一层的时候，是深拷贝；
    扩展运算符(…)；目标对象只有一层的时候，是深拷贝；
深拷贝：是递归拷贝深层次，属性为对象时，深拷贝是新开栈，两个对象指向不同的地址
	结合使用JSON.parse()和JSON.stringify()方法。
	手写遍历递归赋值；
```

#### 52.如何判断this？箭头函数的this是什么

```scss
函数直接调用中的this // 指向window
在对象里调用的情况：//this会指向调用的对象
在构造函数及类中this会指向实例化的对象 
箭头函数不会创建自己的this,它只会从自己的作用域链的上一层继承this
```

#### 53.== 和 ===的区别

```scss
"==“叫做相等运算符，”==="叫做严格运算符。
"=="，等同的意思，两边值类型不同的时候，要先进行类型转换为同一类型后，再比较值是否相等。
  "==="，恒等的意思，不做类型转换，类型不同的结果一定不等。
"==“表示只要值相等即可为真，而”==="则要求不仅值相等，而且也要求类型相同。
```

#### 54.什么是闭包

```scss
闭包(closure)就是能够读取其他函数内部变量的函数
```

#### 55.JavaScript原型，原型链 ? 有什么特点？

```scss
每个对象都有原型（null和undefined除外），你可以把它理解为对象的默认属性和方法
在JS 中，每个对象都有一个指向它的原型（prototype）对象的内部链接。这个原型对象又有自己的原型，直到某个对象的原型为 null 为止，组成这条链的最后一环。这种一级一级的链结构就称为原型链
```

#### 56.typeof()和instanceof()的用法区别

```scss
typeof判断所有变量的类型，返回值有number、string、boolean、function、object、undefined
instanceof用来判断对象
```

#### 57.什么是变量提升

```scss
通常发生在var声明的变量里，就是说当使用var声明一个变量的时候，该变量会被提升到作用域的顶端，但是赋值的部分并不会被提升
```

#### 58.call、apply以及bind函数内部实现是怎么样的

```js
Function.prototype.myCall = function (context) {
    // 判断this的类型，如果不是function 则返回
    if(typeof this !== 'function') return
    // 判断有没有传入上下文，如果没有传入则指向window
    context = context || Window;
    // 在上下文中注入一个属性指向this
    context.fn = this // 改变指向
    // 从第二个参数开始才是要传入的实参列表，截取传入的参数（第一个是传入的要改变的this）
    let args = [...arguments].slice(1)
    // 指行函数
    let res = context.fn(...args)
    // 删除属性，不然污染传入的上下文
    delete context.fn
    return res
}
Function.prototype.myBind = function (context) {
    // 不是函数类型，返回
    if (typeof this !== 'function') return;
    // 记录一下this,因为需要返回一个函数，this会被篡改
    let _self = this
    // 截取实参列表
    let args = Array.prototype.slice.call(arguments, 1)
    return function () {
        // 处理柯里化传入的参数，拼接到bind绑定的后面
        return _self.apply(context, args.concat(Array.prototype.slice.call(arguments)))
    }
}
```

#### 59.为什么会出现setTimeout倒计时误差？如何减少

    setTimeout作为异步任务，在实现倒计时功能的时候，除了执行我们功能的实现代码，还会有主线程对任务队列的读取及执行等过程，这些过程也需要耗费一些时间，所以会因为event loop的机制出现些许误差
    计算误差值、动态调整执行setTimeout的间隔

#### 60.谈谈你对JS执行上下文栈和作用域链的理解

```scss
执行上下文：就是当前 JavaScript 代码被解析和执行时所在环境, JS执行上下文栈可以认为是一个存储函数调用的栈结构。
作用域链：函数执行所需要的变量在当前作用域中找不到的时候，它就会一层一层向上查找，一直找到全局作用域。这种一层一层的关系，就是作用域链
```

#### 61.new的原理是什么？通过new的方式创建对象和通过字面量创建有什么区别？

```scss
new的工作原理：
    1.创建一个空对象，构造函数中的this会指向这个对象
    2.这个新对象会被链接到原型
    3.执行构造函数方法，其属性和方法都会被添加到this引用的对象中
    4.如果构造函数中没有返回新对象，那么返回this，即创建新对象；否则，返回构造函数中返回的对象。
new和字面量创建对象的区别：
    1.字面量创建对象，不会调用Object构造函数，简洁且性能更好；
    2.new Object() 方式创建对象本质上是方法调用，涉及到在proto链中遍历该方法，当找到该方法后，又会生产方法调用必须的 堆栈信息，方法调用结束后，还要释放该堆栈，性能不如字面量的方式。
```

#### 62.prototype 和 proto 区别是什么？

```scss
prototype是每个函数都会具备的一个属性，它是一个指针，指向一个对象，只有函数才有。
proto是主流浏览器上在除null以外的每个对象上都支持的一个属性，它能够指向该对象的原型
```

#### 63.使用ES5实现一个继承？

```js
// ES5构造函数
let Parent = function (name, age) {
    //1.创建一个新对象，赋予this，这一步是隐性的，
    // let this = {};
    //2.给this指向的对象赋予构造属性
    this.name = name;
    this.age = age;
    //3.如果没有手动返回对象，则默认返回this指向的这个对象，也是隐性的
    // return this;
};
const child = new Parent();
```

#### 64.取数组的最大值（ES5、ES6）

```js
// ES5 的写法
    Math.max.apply(null, [14, 3, 77, 30]);
    // ES6 的写法
    Math.max(...[14, 3, 77, 30]);
```

#### 65.ES6新的特性有哪些？

```scss
新的原始类型和变量声明：symbol、let和const、解构赋值 
新的对象和方法 ：Map和Set、对象新特性（拓展运算符(...)三点、assign()和is()）
字符串新方法：includes()：判断字符串是否包含参数字符串，返回boolean值
			startsWith()/endsWith()：判断字符串是否以参数字符串开头或结尾
			padStart()/padEnd()：用参数字符串按给定长度从前面或后面补全字符串，返回新字符串
			repeat()：按指定次数返回一个新的字符串
函数：箭头函数、参数默认值
class（类）：class 作为对象的模板被引入ES6，你可以通过 class 关键字定义类。class 的本质依然是一个函数。
模块导入和导出：import 、export 
异步机制：Promise和Generator
```

#### 66.promise 有几种状态, Promise 有什么优缺点 ?

```scss
pending(进行中)、fulfilled(已成功)、rejected(已失败)
Promise优点：
   统一异步 API：Promise 的一个重要优点是它将逐渐被用作浏览器的异步API，统一现在各种各样的 API，以及不兼容的模式和手法。
   Promise 与事件对比：和事件相比较，Promise 更适合处理一次性的结果。在结果计算出来之前或之后注册回调函数都是可以的，都可以拿到正确的值。Promise 的这个优点很自然。但是，不能使用 Promise 处理多次触发的事件。链式处理是 Promise 的又一优点，但是事件却不能这样链式处理。
   Promise 与回调对比：解决了回调地狱的问题，将异步操作以同步操作的流程表达出来。
   Promise 带来的额外好处是包含了更好的错误处理方式（包含了异常处理），并且写起来很轻松。
Promise缺点
    无法取消Promise，一旦新建它就会立即执行，无法中途取消。
    如果不设置回调函数，Promise内部抛出的错误，不会反应到外部。
    当处于Pending状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。
    Promise 真正执行回调的时候，定义 Promise 那部分实际上已经走完了，所以 Promise的报错堆栈上下文不太友好。
```

#### 67.Promise构造函数是同步还是异步执行，then呢 ?promise如何实现then处理 ?

```scss
promise构造函数是同步执行的，then方法是异步执行的
```

#### 68.Promise和setTimeout的区别 ?

```scss
Promise：微任务
setTimeout：宏任务
Promise会优先于setTimeout执行
```

#### 69.如何实现 Promise.all ?

```js
Promise.all = function(array) {  
  return new Promise(function(resolve, reject) {  
    // 如果数组为空，直接返回  
    if (array.length === 0) {  
      resolve([]);  
    }  
    // 如果有Promise失败，就立即reject  
    var remaining = array.length;  
    var values = [];  
    array.forEach(function(promise, index) {  
      promise.then(function(value) {  
        remaining--;  
        values[index] = value;  
        // 如果有Promise成功，就减少计数  
        if (remaining === 0) {  
          resolve(values);  
        }  
      }, function(reason) {  
        remaining = -1;  
        reject(reason);  
      });  
    });  
  });  
};
```

#### 70.如何实现 Promise.finally ?

```js
class PromiseLike {  
  constructor(executor) {  
    this.status = 'pending';  
    this.value = undefined;  
    this.reason = undefined;  
    this.next = null;  
  
    executor(  
      (result) => {  
        if (this.status === 'pending') {  
          this.status = 'fulfilled';  
          this.value = result;  
          this.finallyFulfilled();  
        } else {  
          // Promise has already been resolved. This should only happen with .then() or .catch()  
          throw new Error('Promise has already been resolved');  
        }  
      },  
      (reason) => {  
        if (this.status === 'pending') {  
          this.status = 'rejected';  
          this.reason = reason;  
          this.finallyRejected();  
        } else {  
          // Promise has already been resolved. This should only happen with .then() or .catch()  
          throw new Error('Promise has already been resolved');  
        }  
      }  
    );  
  }  
  
  then(onFulfilled, onRejected) {  
    if (this.status === 'pending') {  
      const next = new PromiseLike(executor);  
      this.next = next;  
      executor(onFulfilled, onRejected);  
      return next;  
    } else if (this.status === 'fulfilled') {  
      return new Promise((resolve) => {  
        resolve(onFulfilled(this.value));  
      });  
    } else if (this.status === 'rejected') {  
      return Promise.reject(onRejected(this.reason));  
    } else {  
      // Should never happen  
      throw new Error('Promise is in an invalid state');  
    }  
  }  
  
  catch(onRejected) {  
    return this.then(null, onRejected);  
  }  
  
  finally(onFinally) {  
    if (this.status !== 'pending') {  
      const status = this.status;  
      const value = this.value;  
      const reason = this.reason;  
      onFinally();  
      return status === 'fulfilled' ? Promise.resolve(value) : Promise.reject(reason);  
    } else {  
      const next = this.next;  
      if (next) {  
        return next.finally(onFinally);  
      } else {  
        // The promise doesn't have any .then() or .catch() handlers, so the finally() should run after the original promise settles.  
        return Promise.resolve().then(onFinally).then(() => this);  
      }  
    }  
  }  
}
```

#### 71.如何判断img加载完成

```scss
img的complete属性
Load事件
readystatechange事件
```

#### 72.如何阻止冒泡？

```js
event.stopPropagation()
event.preventDefault()
return false;
```

#### 73.如何阻止默认事件？

```js
preventDefault
stopPropagation
return false;
return true;
```

#### 74.ajax请求时，如何解释json数据

```scss
方法1： 设置 ajax 方法的 dataType 属性为 ‘json’。
方法2： 使用 JSON 对象的 parse() 方法解析。
方法3： 使用 eval() 方法，var dataObj = eval("("+data+")")
```

#### 75.json和jsonp的区别?

```scss
JSON是一种数据交换格式，而JSONP是一种非官方跨域数据交互协
```

#### 76.如何用原生js给一个按钮绑定两个onclick事件？

```scss
addEventListener 事件监听 绑定多个事件
```

#### 77.拖拽会用到哪些事件？

```scss
dragstart：按下鼠标键并开始移动时触发
dragend：元素拖拽停止时触发
drag：在元素拖拽过程中持续触发----相似与mousemove
dragenter：当拖拽对象进入投放区时触发
dragover ：拖拽对象在投放区内移动时持续触发
dragleave：元素被拖出了投放区时触发
drop：拖拽对象投放在投放区时触发
```

#### 78.document.write和innerHTML的区别

```scss
document.write会重绘整个页面，而innerHTML是可以重绘页面的某一部分
write是DOM方法,向文档写入HTML表达式或JavaScript代码，可列出多个参数，参数被顺序添加到文档中 ；
innerHTML是DOM属性,设置或返回调用元素开始结束标签之间的HTML元素
两者都可向页面输出内容
```

#### 79.jQuery的事件委托方法bind 、live、delegate、on之间有什么区别？

```scss
bind定义和用法：主要用于给选择到的元素上绑定特定事件类型的监听函数；
live定义和用法：主要用于给选择到的元素上绑定特定事件类型的监听函数；
delegate定义和用法：将监听事件绑定在就近的父级元素上
on定义和用法：将监听事件绑定到指定元素上
```

#### 80.浏览器是如何渲染页面的？

```scss
解析html代码，生成DOM tree
解析css代码，生成CSSOM tree
通过DOM tree 和 CSSOM tree 生成 Render tree
Layout（布局），计算Render tree中各个节点的位置及精确大小
Painting（绘制），将render tree渲染到页面上
```

#### 81.$(document).ready()方法和window.onload有什么区别？

window.onload必须等到页面内包括图片的所有元素加载完毕后才能执行。
$(document).ready()是DOM结构绘制完毕后就执行，不必等到加载完毕。
window.onload不能同时编写多个，如果有多个window.onload方法，只会执行一个
$(document).ready()可以同时编写多个，并且都可以得到执行
window.onload没有简化写法 ;$(document).ready(function(){})可以简写成$(function(){});

#### 82. jquery中$.get()提交和$.post()提交有区别吗？

相同点：
	都是异步请求的方式来获取服务端的数据。
不同点：
    请求方式不同：$.get() 方法使用GET方法来进行异步请求的。$.post() 方法使用POST方法来进行异步请求的。
    参数传递方式不同：GET请求会将参数跟在URL后进行传递，而POST请求则是作为HTTP消息的实体内容发送给Web服务器的，这种传递是对用户不可见的。
    数据传输大小不同：GET方式传输的数据大小不能超过2KB，而POST要大的多。
    安全问题：GET方式请求的数据会被浏览器缓存起来，因此有安全问题。

#### 83.对前端路由的理解？前后端路由的区别？

​		很重要的一点是页面不刷新，前端路由就是把不同路由对应不同的内容或页面的任务交给前端来做，每跳转到不同的URL都是使用前端的锚点路由.随着（SPA）单页应用的不断普及，前后端开发分离，目前项目基本都使用前端路由，在项目使用期间页面不会重新加载。

#### 84.手写一个类的继承

```js
	class Fa {
        constructor(name) {
            this.name = name
        }
        set() {
            return [...this.name]
        }
    }
    class Sa extends Fa {
        constructor(name, age) {
            super(name)
            this.age = age
        }
    }
    var dd = new Sa(123, 456)
    dd.set()                //[1,2,3]
```

#### 85.XMLHttpRequest：XMLHttpRequest.readyState;状态码的意思

0：请求未初始化，还没有调用 open() 。
1：请求已经建立，但是还没有发送，还没有调用 send() 。
2：请求已发送，正在处理中（通常现在可以从响应中获取内容头）。
3：请求在处理中；通常响应中已有部分数据可用了，没有全部完成。
4：响应已完成；您可以获取并使用服务器的响应了。

#### 86.JavaScript如何实现异步编程，有哪几种方式？

```scss
1.回调函数（Callback Functions）：这是最基础的异步编程方式，但它的缺点是在某些情况下可能导致“回调地狱”（回调函数嵌套回调函数，使代码变得难以理解和维护）。
2.Promises：Promises是一种更优雅的异步编程方式，它返回一个表示异步操作结果的对象。Promises有三种状态：pending（待定），fulfilled（已实现），rejected（已拒绝）。使用Promises可以使代码更加整洁，并且可以避免回调地狱。
3.Async/Await：这是基于Promises的一种更简洁的异步编程方式。async关键字表示一个函数是异步的，await关键字在一个async函数中，用于等待Promise的结果。
4.观察者模式或发布-订阅模式：在这种模式中，有一些对象（观察者）订阅一个对象（主题）的某种状态。当这个状态改变时，所有订阅了该状态的对象都会收到通知。这也是一种异步编程的模式。
5.生成器：生成器是ES6的一个新特性，它可以暂停和恢复执行上下文，这使得我们可以用它来编写类似于同步的异步代码。不过，生成器并不直接处理异步操作，而是通过暂停和恢复执行上下文来模拟异步操作。
```

#### 87.解释JavaScript中的“this”关键字是如何工作的？

​    “this”是一个关键字，它在函数被调用时被自动设定。其值取决于函数如何被调用，这取决于调用的上下文。

在函数被直接调用时，“this”通常指的是全局对象（在浏览器中是window，在Node.js中是global）。但是，当一个函数作为对象的方法被调用时，“this”指向的是调用它的对象，例如：

```javascript
let obj = {  
  value: 10,  
  increment: function() {  
    this.value++; // "this" here refers to obj  
    console.log(this.value);  
  }  
};  
obj.increment(); // 输出：11
```

在上述代码中，`increment`函数是 `obj`对象的一个方法，所以当它被调用时，“this”指向的是 `obj`。

然而，在函数作为普通函数（不是对象的方法）被调用时，“this”的值是全局对象（在浏览器中是window，在Node.js中是global），除非在严格模式下（'use strict'），此时“this”是undefined。例如：

```javascript
function func() {  
  console.log(this);  
}  
  
func(); // 输出：window或global
```

此外，可以使用call、apply或bind等方法显式设置函数的“this”。这些方法允许你手动指定函数内部的“this”应该指向哪个对象。例如：

```javascript
let obj = {  
  value: 10,  
  increment: function(amount) {  
    this.value += amount;   
    console.log(this.value);  
  }  
};  
  
let anotherObj = {value: 5};  
obj.increment.call(anotherObj, 2); // 输出：7，因为在这个上下文中，“this”指向的是anotherObj
```

#### 88.解释JavaScript中的构造函数是什么，并给出一个例子？

​        构造函数是用来创建对象的函数。它们被设计用于初始化新创建的对象，并给这些对象设置属性和方法。

构造函数通常以大写字母开头，以区别于其他非构造函数。这是一种约定，虽然不是强制的，但为了保持一致性，大多数开发者都会遵循这个规则。

示例：

```javascript
function Car(make, model, year) {  
  this.make = make;  
  this.model = model;  
  this.year = year;  
}  
// 使用构造函数创建对象  
let myCar = new Car('Toyota', 'Corolla', 2020);  
// 输出结果：Toyota Corolla 2020  
console.log(myCar.make + ' ' + myCar.model + ' ' + myCar.year);
```

#### 89.JavaScript中的“undefined”和“null”有什么区别

​      “undefined”和“null”都表示缺乏值或没有值的情况。

1. 语义不同：`null`通常表示我们知道这个变量应该持有某个值，但现在它没有值。`undefined`则表示我们不知道这个变量应该持有什么样的值。也就是说，`null`是“有意识的空”，而 `undefined`是“无意识的空”。
2. 在JavaScript中的表现：在JavaScript中，`undefined`和 `null`在某些情况下会被视为相等（即，`null == undefined`会返回 `true`），但实际上它们是不同的。使用 `===` 运算符可以区分它们，因为 `undefined` 是 `undefined`，而 `null` 是 `null`。
3. 类型检查：使用 `typeof` 运算符可以检查一个变量的类型。如果变量是 `undefined`，`typeof` 会返回 "undefined"，如果变量是 `null`，`typeof` 会返回 "object"，这表明 `null` 被视为一种特殊的对象类型。
4. 在JavaScript引擎内部：在JavaScript引擎的实现中，`null`通常用于表示对象引用的“终止”，或者表示某种“无”或“不存在”的情况。而 `undefined`则常常表示变量未被初始化。

#### 90.请解释JavaScript中的事件循环机制

1.检查调用栈是否为空。如果为空，继续等待事件队列中的新事件。
2.检查到事件队列中有新的事件（如用户交互、网络请求等）。
3.将新事件添加到调用栈的顶部。
4.执行调用栈中的任务，直到栈为空或遇到同步任务（如await、yield等）。
5.如果在执行过程中遇到了同步任务，将同步任务直接添加到调用栈中。
6.继续从事件队列中取出新事件，重复上述过程。

#### 91.JavaScript中的提升(Hoisting)是什么？请举例说明。

​	提升（Hoisting）是指变量和函数声明在代码被解析时被移动到它们所在作用域的顶部。