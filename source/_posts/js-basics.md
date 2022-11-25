---
title: javascript基础
date: 2021-12-20 09:38:22
tags: basics
comment: 'valine'
categories: 
- js
---

# js数据类型

### ===与==

 == 自动转换数据类型再比较；
 === 不转换类型  （更好）
 例外：
 NaN === NaN  // false

### 字符串

使用反引号来表示多行字符串



```js
var a = `这是一个
多行
字符串`;
```

字符串不可变：使用`a[0] = 'X'`并没有改变效果

### 数组

直接给array的length赋新值会改变Array的大小;
 length变大则新增元素为undefined;变小则删除超出长度的元素;

数组方法



```js
slice   // 不加参数可以用于复制数组，不改变本身
pop
push
unshift // 在头部添加
shift   // 在头部删除
sort    // 字母排序
reverse // 反转
splice  // 从指定索引删除若干元素，在从该位置添加若干元素
concat  // 拼接,返回新Array
join    // 使用指定字符拼接数组元素 ==> String
```

### Map 键值对结构，大数据量时具有极快的查找速度

根据key值迅速获取对应value



```js
var m = new Map([['Michael', 95], ['Bob', 75], ['Tracy', 85]]);
m.get('Michael'); // 95
m.set('Adam', 67); // 添加新的key-value
m.set('Bob', 59);
m.has('Adam'); // 是否存在key 'Adam': true
m.get('Adam'); // 67
m.delete('Adam'); // 删除key 'Adam'
m.get('Adam'); // undefined
```

### Set 只有非重复key，没有value值



```js
var s = new Set([1, 2, 3, 3, '3']);
s; // Set {1, 2, 3, "3"}
// 添加元素
s.add(4);
s; // {1, 2, 3, 4}
// 删除元素
s.delete(3);
```

### iterable类型

包括Array、Map、Set等；
 for。。。in
 循环对象的属性名称key；一个Array数组实际上也是一个对象，它的每个元素的索引被视为一个属性。
 for。。。of
 循环对象键值对中的value；更适合遍历没有key的集合，如Set；
 forEach方法   （更好）



```js
a.forEach(function (element, index, array) {
    // element: 指向当前元素的值
    // index: 指向当前索引
    // array: 指向Array对象本身
    alert(element);
});

// Set与Array类似，但Set没有索引，因此回调函数的前两个参数都是元素本身：
// Map的回调函数参数依次为value、key和map本身：
```

### 函数arguments参数

它只在函数内部起作用，并且永远指向当前函数的调用者传入的所有参数。arguments类似Array但它不是一个Array：
 利用arguments，你可以获得调用者传入的所有参数。即使函数不定义任何参数，还是可以拿到参数的值

当传入参数个数超过函数定义的参数个数，则将多余参数传入rest参数。



```js
// rest参数只能写在最后，前面用...标识
// 传入的参数先绑定a、b，多余的参数以数组形式交给变量rest
// 所以，不再需要arguments我们就获取了全部参数。
function foo(a, b, ...rest) {
    console.log('a = ' + a);
    console.log('b = ' + b);
    console.log(rest);
}

foo(1, 2, 3, 4, 5);
// 结果:
// a = 1
// b = 2
// Array [ 3, 4, 5 ]

foo(1);
// 结果:
// a = 1
// b = undefined
// Array []
```

### 全局对象 window

全局作用域的变量实际绑定到window的一个属性



```js
'use strict';

var course = 'Learn JavaScript';
alert(course); // 'Learn JavaScript'
alert(window.course); // 'Learn JavaScript'

// 全局变量course 等同于 window.course
// alert函数也是window的一个变量
```

减少全局变量的冲突：声明一个唯一的全局变量{}；然后把所有变量和函数都绑定到这个唯一一个全局变量上。

### 局部作用域

for循环中，var i可以在循环块外部被引用；ES6的let i则解决了这个问题。



```js
'use strict';

function foo() {
    for (var i=0; i<100; i++) {
        //
    }
    i += 100;      // 仍然可以引用变量i
}


'use strict';

function foo() {
    var sum = 0;
    for (let i=0; i<100; i++) {
        sum += i;
    }
    i += 1;       // SyntaxError
}
```

### this丢失问题

对象方法中的this，必须由对象进行调用该方法才能获取this；
 对象方法中的函数，this指向全局对象window；
 解决方法1：  that



```js
// 保存当前this  ===> that
'use strict';

var xiaoming = {
    name: '小明',
    birth: 1990,
    age: function () {
        var that = this; // 在方法内部一开始就捕获this
        function getAgeFromBirth() {
            var y = new Date().getFullYear();
            return y - that.birth; // 用that而不是this
        }
        return getAgeFromBirth();
    }
};

xiaoming.age(); // 25
```

解决方法2：  apply与call



```js
// 使用apply或call，控制函数的this指向传入的首个参数
function getAge() {
    var y = new Date().getFullYear();
    return y - this.birth;
}

var xiaoming = {
    name: '小明',
    birth: 1990,
    age: getAge
};

xiaoming.age(); // 25

// apply()把参数打包成Array再传入；
getAge.apply(xiaoming, []); // 25, this指向xiaoming, 参数为空
// 把参数按顺序传入。
getAge.call(xiaoming)
```

解决方法3： IFEE思想，bind(this)

### 高阶函数

```
函数参数`指向`函数
```

#### map逐一映射



```js
// *****
// “把f(x)作用在Array的每一个元素并把结果生成一个新的Array”。
// *****
function pow(x) {
    return x * x;
}

var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9];
arr.map(pow); // [1, 4, 9, 16, 25, 36, 49, 64, 81]
```

#### reduce瀑布映射

原理：[x1, x2, x3, x4].reduce(f) = f(f(f(x1, x2), x3), x4)



```js
// *****
// 把结果继续和序列的下一个元素做累积计算
// *****

// [1, 3, 5, 7, 9]变换成整数13579，reduce()也能派上用场：

var arr = [1, 3, 5, 7, 9];
arr.reduce(function (x, y) {
    return x * 10 + y;
}); // 13579
```

#### filter过滤

filter()把传入的函数依次作用于每个元素，然后根据返回值是true还是false决定保留还是丢弃该元素。



```js
// 利用filter，可以巧妙地去除Array的重复元素：

'use strict';

var r,
    arr = ['apple', 'strawberry', 'banana', 'pear', 'apple', 'orange', 'orange', 'strawberry'];

r = arr.filter(function (element, index, self) {
    // indexOf总是返回第一个元素的位置，后续的重复元素位置与indexOf返回的位置不相等，被过滤
    return self.indexOf(element) === index;
});

alert(r.toString());
```

### sort 排序

`sort()方法会直接对Array进行修改，它返回的结果仍是当前Array：`   
 默认把所有元素先转换为String再排序，字符串根据首个字符的ASCII码进行排序
 sort的自定义排序中，return -1不对调；return 1对调；



```js
// 升序排列
var arr = [10, 20, 1, 2];
arr.sort(function (x, y) {
    if (x < y) {
        return -1;
    }
    if (x > y) {
        return 1;
    }
    return 0;
}); // [1, 2, 10, 20]
```

### 箭头函数



```js
x => x * x   

等同于  

function (x) {
    return x * x;
}
```

箭头函数相当于匿名函数
 格式一，只包含一个表达式，可以省略{ ... }和return
 格式二，包含多条语句，不能省略{ ... }和return：

#### 参数



```js
// 两个参数:
(x, y) => x * x + y * y

// 无参数:
() => 3.14

// 可变参数:
(x, y, ...rest) => {
    var i, sum = x + y;
    for (i=0; i<rest.length; i++) {
        sum += rest[i];
    }
    return sum;
}
```

#### 返回对象

返回对象时存在语法冲突{} 所以改为({foo:x})

#### this作用域

箭头函数修复了this的指向，this总是指向词法作用域，也就是外层调用者obj：
 也就是说，在对象（第一层）的方法（第二层）的函数（第三层）中，仍然可以用this调用到对象的属性值；



```js
var obj = {
    birth: 1990,
    getAge: function () {
        var b = this.birth; // 1990
        var fn = () => new Date().getFullYear() - this.birth;    //this指向obj对象
        return fn();
    }
};
obj.getAge(); // 25
```

#### call()与apply()

由于this在箭头函数中已经按照词法作用域绑定了，所以，用call()或者apply()调用箭头函数时，无法对this进行绑定，即传入的第一个参数被忽略，直接传入参数即可



```js
var obj = {
    birth: 1990,
    getAge: function (year) {
        var b = this.birth; // 1990
        var fn = (y) => y - this.birth; // this.birth仍是1990
        return fn.call({birth:2000}, year);
        // 原本需要写作fn.call(this, {birth:2000}, year)
    }
};
obj.getAge(2015); // 25
```

### generator生成器

yield 用于return之前的多次返回
 generator



```js
// 定义
function* foo(x) {
    yield x + 1;
    yield x + 2;
    return x + 3;
}

// 创建generator对象   与函数调用不同，不能直接调用
var f = foo(3);    // {[[GeneratorStatus]]: "suspended"}

// 实际获取返回值   done属性表示执行完毕
f.next();    // {value: 4, done: false}
f.next();    // {value: 5, done: false}
f.next();    // {value: 6, done: true}
f.next();    // {value: undefined, done: true}


// 直接用for ... of循环迭代generator对象，这种方式不需要我们自己判断done
for (var x of f(3)) {
    console.log(x); // 依次输出4,5,6
}
```

generator的优点：
 1.因为generator可以在执行过程中多次返回，所以它看上去就像一个可以记住执行状态的函数，利用这一点，写一个generator就可以实现需要用面向对象才能实现的功能。

2.把异步回调代码变成形似同步代码（主要是美观，实际仍未异步）



```js
// 原来的ajax写法
ajax('http://url-1', data1, function (err, result) {
    if (err) {
        return handle(err);
    }
    ajax('http://url-2', data2, function (err, result) {
        if (err) {
            return handle(err);
        }
        ajax('http://url-3', data3, function (err, result) {
            if (err) {
                return handle(err);
            }
            return success(result);
        });
    });
});

// 使用generator美化之后
try {
    r1 = yield ajax('http://url-1', data1);
    r2 = yield ajax('http://url-2', data2);
    r3 = yield ajax('http://url-3', data3);
    success(r3);
}
catch (err) {
    handle(err);
}
```

### 对象



```js
// 标准对象typeof 123; // 'number'typeof NaN; // 'number'typeof 'str'; // 'string'typeof true; // 'boolean'typeof undefined; // 'undefined'typeof Math.abs; // 'function'typeof null; // 'object'typeof []; // 'object'typeof {}; // 'object'// 包装对象  拆包前是Object；解包后是普通数据类型var n = new Number(123); // 123,生成了新的包装类型var b = new Boolean(true); // true,生成了新的包装类型var s = new String('str'); // 'str',生成了新的包装类型
```

### 正则表达式

regExp.test(str);   返回值：false/trues
 用途：

1. 切割字符串



```js
'a,b;; c  d'.split(/[\s\,\;]+/); // ['a', 'b', 'c', 'd']
```

1. 分组，提取子串



```js
var re = /^(\d{3})-(\d{3,8})$/;
re.exec('010-12345'); // ['010-12345', '010', '12345']
re.exec('010 12345'); // null
```

### JSON

#### 字符串化 stringify



```js
var xiaoming = {
    name: '小明',
    age: 14,
    gender: true,
    height: 1.65,
    grade: null,
    'middle-school': '\"W3C\" Middle School',
    skills: ['JavaScript', 'Java', 'Python', 'Lisp']
};

// 原始字符串显示
JSON.stringify(xiaoming);

// 缩进显示 
JSON.stringify(xiaoming, null, '  ');

// 传入Array，输出指定的属性
JSON.stringify(xiaoming, ['name', 'skills'], '  ');

// 传入一个函数，处理对象的每个键值
function convert(key, value) {
    if (typeof value === 'string') {
        return value.toUpperCase();
    }
    return value;
}

JSON.stringify(xiaoming, convert, '  ');

// JSON数据添加toJSON属性，控制JSON应该序列化的数据
toJSON: function () {
        return { // 只输出name和age，并且改变了key：
            'Name': this.name,
            'Age': this.age
        };
    }

JSON.stringify(xiaoming); // '{"Name":"小明","Age":14}'
```

#### 反序列化 JSON.parse

JSON.parse()还可以接收一个函数，用来转换解析出的属性：



```js
JSON.parse('{"name":"小明","age":14}', function (key, value) {
    // 把number * 2:
    if (key === 'name') {
        return value + '同学';
    }
    return value;
}); // Object {name: '小明同学', age: 14}
```

### 构造函数

构造函数与普通函数的声明几乎没有区别。
 但是必须通过new来调用构造函数，它绑定的this指向新创建的对象，并默认返回this，也就是说，不需要在最后写return this;
 如果不写new，这就是一个普通函数，它返回undefined。



```js
// 生成的每个实例都会有独立的name属性与hello属性；hello的函数代码重复，浪费内存空间
function Student(name) {
    this.name = name;
    this.hello = function () {
        alert('Hello, ' + this.name + '!');
    }
}

//  把hello函数放在prototype下，可以让不同的实例共享同个函数，节省内存空间
function Student(name) {
    this.name = name;
}

Student.prototype.hello = function () {
    alert('Hello, ' + this.name + '!');
};
```

### 原型继承(其实ES6的class优化更容易理解、好用)

**重点理解！！！prototype指向的是一个对象，而不是prototype指向prototype属性**

`new PrimaryStudent() ----> PrimaryStudent.prototype ----> Object.prototype ----> null`   
 必须想办法把原型链修改为：
 `new PrimaryStudent() ----> PrimaryStudent.prototype ----> Student.prototype ----> Object.prototype ----> null`   
 不能直接改变指向；
 必须借助一个中间对象来实现正确的原型链，这个中间对象的原型要指向Student.prototype，中间对象可以用一个空函数F来实现



```js
// PrimaryStudent构造函数:
function PrimaryStudent(props) {
    // 调用了Student构造函数，绑定this变量
    // 但是PrimaryStudent的prototype并不指向Student；这并不是继承
    Student.call(this, props);    
    this.grade = props.grade || 1;
}

// 空函数F:
function F() {
}

// 把F的原型指向Student.prototype:
F.prototype = Student.prototype;

// 把PrimaryStudent的原型指向一个新的F对象，F对象的原型正好指向Student.prototype:
PrimaryStudent.prototype = new F();

// 把PrimaryStudent原型的构造函数修复为PrimaryStudent:
PrimaryStudent.prototype.constructor = PrimaryStudent;

// 继续在PrimaryStudent原型（就是new F()对象）上定义方法：
PrimaryStudent.prototype.getGrade = function () {
    return this.grade;
};

// 创建xiaoming:
var xiaoming = new PrimaryStudent({
    name: '小明',
    grade: 2
});
xiaoming.name; // '小明'
xiaoming.grade; // 2

// 验证原型:
xiaoming.__proto__ === PrimaryStudent.prototype; // true
xiaoming.__proto__.__proto__ === Student.prototype; // true

// 验证继承关系:
xiaoming instanceof PrimaryStudent; // true
xiaoming instanceof Student; // true
```



```js
// 封装空函数继承过程
function inherits(Child, Parent) {
    var F = function () {};
    F.prototype = Parent.prototype;
    Child.prototype = new F();
    Child.prototype.constructor = Child;
}

// 实现原型继承链:
inherits(PrimaryStudent, Student);
```

### class继承 ===> 原型继承的优化



```js
// 使用构造函数
function Student(name) {
    this.name = name;
}

Student.prototype.hello = function () {
    alert('Hello, ' + this.name + '!');
}

// 使用class实现
class Student {
    constructor(name) {
        this.name = name;
    }

    hello() {
        alert('Hello, ' + this.name + '!');
    }
}

// 实例化
var xiaoming = new Student('小明');
xiaoming.hello();
```



```js
// 使用extends关键字实现继承
class PrimaryStudent extends Student {
    constructor(name, grade) {
        super(name); // 记得用super调用父类的构造方法!
        this.grade = grade;
    }

    myGrade() {
        alert('I am at grade ' + this.grade);
    }
}
```

### 浏览器对象

#### window对象

window对象不但充当全局作用域，而且表示浏览器窗口。

window对象有innerWidth和innerHeight属性，可以获取浏览器窗口的内部宽度和高度。内部宽高是指除去菜单栏、工具栏、边框等占位元素后，用于显示网页的净宽高。

#### navigator对象

navigator对象表示浏览器的信息，最常用的属性包括：

navigator.appName：浏览器名称；
 navigator.appVersion：浏览器版本；
 navigator.language：浏览器设置的语言；
 navigator.platform：操作系统类型；
 navigator.userAgent：浏览器设定的User-Agent字符串。

#### screen对象

screen对象表示屏幕的信息，常用的属性有：

screen.width：屏幕宽度，以像素为单位；
 screen.height：屏幕高度，以像素为单位；
 screen.colorDepth：返回颜色位数，如8、16、24。

#### location对象

location对象表示当前页面的URL信息。例如，一个完整的URL：

[http://www.example.com:8080/path/index.html?a=1&b=2#TOP](https://link.jianshu.com?t=http://www.example.com:8080/path/index.html?a=1&b=2#TOP)
 可以用location.href获取。要获得URL各个部分的值，可以这么写：

location.protocol; // 'http'
 location.host; // '[www.example.com'](https://link.jianshu.com?t=http://www.example.com')
 location.port; // '8080'
 location.pathname; // '/path/index.html'
 location.search; // '?a=1&b=2'
 location.hash; // 'TOP'

要加载一个新页面：location.assign()
 重新加载当前页面，location.reload()

#### document对象

document对象表示当前页面。由于HTML在浏览器中以DOM形式表示为树形结构，document对象就是整个DOM树的根节点。

document对象还有一个cookie属性，可以获取当前页面的Cookie。

Cookie是由服务器发送的key-value标示符。因为HTTP协议是无状态的，但是服务器要区分到底是哪个用户发过来的请求，就可以用Cookie来区分。当一个用户成功登录后，服务器发送一个Cookie给浏览器，例如user=ABC123XYZ(加密的字符串)...，此后，浏览器访问该网站时，会在请求头附上这个Cookie，服务器根据Cookie即可区分出用户。

Cookie还可以存储网站的一些设置，例如，页面显示的语言等等。

JavaScript可以通过document.cookie读取到当前页面的Cookie：

> 

为了解决这个问题，服务器在设置Cookie时可以使用httpOnly，设定了httpOnly的Cookie将不能被JavaScript读取。这个行为由浏览器实现，主流浏览器均支持httpOnly选项，IE从IE6 SP1开始支持。
 为了确保安全，服务器端在设置Cookie时，应该始终坚持使用httpOnly。
 效果==> 保证浏览器的document对象中就看不到cookie，不能通过客户端脚本访问；只能通过服务器端的http请求

### DOM更新、插入、删除

#### 修改节点

innerHTML： 以HTML的形式插入代码段，可更新节点的结构；
 innerTEXT： 以字符串的形式插入到节点，内含的html标签失效；
 `element.style.color = 'red';`   通过DOM节点的style属性修改css

#### 添加节点

`document.createElement('p'); element.appendChild(newElement)`   如果新节点已存在，不是简单在尾部添加，而是剪切该元素到目标位置；如果是一直创建新标签，则一直重复插入；
 `document.createElement('p'); element.insertBefore(newElement, referenceElement);`    如果新节点已存在，就不是简单在节点前添加，而是剪切该元素到目标位置；同append；

#### 删除节点



```js
找到目标删除节点，寻至父节点，删除子节点； 被删除的节点仍然保存在内存中，可以再次插入 
// 拿到待删除节点:
var self = document.getElementById('to-be-removed');
// 拿到父节点:
var parent = self.parentElement;
// 删除:
var removed = parent.removeChild(self);
removed === self; // true
```

### 表单

使用onsubmit 与type="submit"进行提交表单；使用click会扰乱form的正常提交



```html
<!-- HTML -->
<form id="test-form" onsubmit="return checkForm()">
    <input type="text" name="test">
    <button type="submit">Submit</button>
</form>

<script>
function checkForm() {
    var form = document.getElementById('test-form');
    // 可以在此修改form的input...
    // 继续下一步:
    return true;
    // return true来告诉浏览器继续提交
    // return false，浏览器将不会继续提交form，这种情况通常对应用户输入有误，提示用户错误信息后终止提交form。
}
</script>
```

### HTML5中的File API允许JS读取文件内容

html5提供了 `File` 与 `FileReader` 两个主要对象，可以获得文件信息并读取文件。
 File读取文件属性信息； FileReader读取文件内部数据（比如图片的base64）



```js
// fileInput：选择文件的按钮； info：读取的文件属性信息；  preview：页面中的预览框  
var fileInput = document.getElementById('test-image-file'),
    info = document.getElementById('test-file-info'),
    preview = document.getElementById('test-image-preview');
// 监听change事件:
fileInput.addEventListener('change', function () {
    // 清除背景图片:
    preview.style.backgroundImage = '';
    // 检查文件是否选择:
    if (!fileInput.value) {
        info.innerHTML = '没有选择文件';
        return;
    }
    // 获取File引用:
    var file = fileInput.files[0];
    // 获取File信息:
    info.innerHTML = '文件: ' + file.name + '<br>' +
                     '大小: ' + file.size + '<br>' +
                     '修改: ' + file.lastModifiedDate;
    if (file.type !== 'image/jpeg' && file.type !== 'image/png' && file.type !== 'image/gif') {
        alert('不是有效的图片文件!');
        return;
    }
    // 读取文件:
    var reader = new FileReader();
    reader.onload = function(e) {
        var
            data = e.target.result; // 'data:image/jpeg;base64,/9j/4AAQSk...(base64编码)...'            
        preview.style.backgroundImage = 'url(' + data + ')';
    };
    // 以DataURL的形式读取文件:
    reader.readAsDataURL(file);
});
```

### AJAX

在现代浏览器上写AJAX主要依靠XMLHttpRequest对象
 根据状态码判断响应进度



```js
// 原生AJAX的写法

function success(text) {
    var textarea = document.getElementById('test-response-text');
    textarea.value = text;
}

function fail(code) {
    var textarea = document.getElementById('test-response-text');
    textarea.value = 'Error code: ' + code;
}

var request = new XMLHttpRequest(); // 新建XMLHttpRequest对象

// 定义request的响应回调函数
request.onreadystatechange = function () { // 状态发生变化时，函数被回调
    if (request.readyState === 4) { // 成功完成
        // 判断响应结果:
        if (request.status === 200) {
            // 成功，通过responseText拿到响应的文本:
            return success(request.responseText);
        } else {
            // 失败，根据响应码判断失败原因:
            return fail(request.status);
        }
    } else {
        // HTTP请求还在继续...
    }
}

// 发送请求:
request.open('GET', '/api/categories');
request.send();

// XMLHttpRequest对象的open()方法有3个参数，第一个参数指定是GET还是POST，第二个参数指定URL地址，第三个参数指定是否使用异步，默认是true，所以不用写。
// 最后调用send()方法才是真正发送请求。
// GET请求不需要参数，POST请求需要把body部分以字符串或者FormData对象传进去。

alert('请求已发送，请等待响应...');

// 使用Promise的简化写法
// ajax函数将返回Promise对象:
function ajax(method, url, data) {
    var request = new XMLHttpRequest();
    return new Promise(function (resolve, reject) {
        request.onreadystatechange = function () {
            if (request.readyState === 4) {
                if (request.status === 200) {
                    resolve(request.responseText);
                } else {
                    reject(request.status);
                }
            }
        };
        request.open(method, url);
        request.send(data);
    });
}

var p = ajax('GET', '/api/categories');
p.then(function (text) { // 如果AJAX成功，获得响应内容
    log.innerText = text;
}).catch(function (status) { // 如果AJAX失败，获得响应代码
    log.innerText = 'ERROR: ' + status;
});
```

#### 跨域请求

大多数浏览器只允许在js中请求域名、协议、端口相同的URL；因此存在跨域问题。
 解决方法：
 1.使用flash插件发送http请求；必须安装flash，不考虑。
 2.在同源域名下架设一个代理服务器来转发，JavaScript负责把请求发送到代理服务器，代理服务器再把结果返回，这样就遵守了浏览器的同源策略。这种方式麻烦之处在于需要服务器端额外做开发。
 3.JSONP，但只能用GET请求，并且要求返回JavaScript。这种方式跨域实际上是利用了浏览器允许跨域引用JavaScript资源；通常以函数调用的形式返回。



```js
以163的股票查询URL为例，对于URL：http://api.money.126.net/data/feed/0000001,1399001?callback=refreshPrice，你将得到如下返回(一个带参的函数调用)：

refreshPrice({"0000001":{"code": "0000001", ... });
因此我们需要首先在页面中准备好回调函数：

function refreshPrice(data) {
    var p = document.getElementById('test-jsonp');
    p.innerHTML = '当前价格：' +
        data['0000001'].name +': ' + 
        data['0000001'].price + '；' +
        data['1399001'].name + ': ' +
        data['1399001'].price;
}

最后用getPrice()函数触发：通过新建script标签，访问API，调用refreshPrice函数，使用自定义的refreshPrice渲染新数据

function getPrice() {
    var js = document.createElement('script'),
        head = document.getElementsByTagName('head')[0];
    js.src = 'http://api.money.126.net/data/feed/0000001,1399001?callback=refreshPrice';
    head.appendChild(js);
}
```

4.CORS （Cross-Origin Resource Sharing）是HTML5规范定义的如何跨域访问资源。
 跨域能否成功，取决于对方服务器是否愿意给你设置一个正确的Access-Control-Allow-Origin，决定权始终在对方手中。

### Promise 承诺将来会执行

效果： 把原本的一种回调细分为两种回调



```js
// 变量p1是一个Promise对象，它负责执行test函数。test函数在内部是异步执行的
// 当test函数执行成功时，我们告诉Promise对象,如果成功，执行.then()
// 当test函数执行失败时，我们告诉Promise对象，如果失败，执行.catch()
// 通过test函数中的resolve与reject来判断函数执行成功与失败  


// 链式简写
new Promise(test).then(function (result) {
    console.log('成功：' + result);
}).catch(function (reason) {
    console.log('失败：' + reason);
});


new Promise(function (resolve, reject) {
    log('start new Promise...');
    var timeOut = Math.random() * 2;
    log('set timeout to: ' + timeOut + ' seconds.');
    setTimeout(function () {
        if (timeOut < 1) {
            log('call resolve()...');
            resolve('200 OK');
        }
        else {
            log('call reject()...');
            reject('timeout in ' + timeOut + ' seconds.');
        }
    }, timeOut * 1000);
}).then(function (r) {
    console.log('Done: ' + r);
}).catch(function (reason) {
    console.log('Failed: ' + reason);
});
```

#### Promise串行执行异步任务



```js
job1.then(job2).then(job3).catch(handleError);

// job1、2、3都是Promise对象
```

#### Promise 并行执行多个异步任务



```js
var p1 = new Promise(function (resolve, reject) {
    setTimeout(resolve, 500, 'P1');
});
var p2 = new Promise(function (resolve, reject) {
    setTimeout(resolve, 600, 'P2');
});


// 并行执行异步任务   Promise.all() 以数组形式返回数据
// 同时执行p1和p2，并在它们都完成后执行then:
Promise.all([p1, p2]).then(function (results) {
    console.log(results); // 获得一个Array: ['P1', 'P2']
});


// 多个异步任务竞争 ===> 为了容错，只需要获得先返回的结果即可；后完成的任务仍在执行，但执行结果将被丢弃
Promise.race([p1, p2]).then(function (result) {
    console.log(result); // 'P1'
})
```

### jQuery

`jQuery === $`  // 本质上是function，而function也是对象，于是$可以直接调用，也有很多其他属性。

组合查找： `$('input[name=email]')`   
 多项选择： `$('p.red,p.green'); // 把<p class="red">和<p class="green">都选出来`

#### 查找



```js
.find('')
.next()  // 可加参数，也可不加
.prev()  // 可加参数，也可不加
```

#### 过滤



```js
// 以下的[] 指若干DOM节点
[].filter('')  // 过滤掉不符合过滤条件的
[].filter(function(){ 
        return this.innerHTML.indexOf('S') === 0
        })   // 仅保留S开头的节点
() => [].map(function(){
    return this.innerHTML;
}).get();   // 用get()拿到包含string的Array

[].first()     //获取若干DOM节点的首个节点
[].last()      //获取若干DOM节点的末尾节点
[].slice()     //截取若干DOM节点的若干节点
```

#### 修改DOM 与 CSS 与 添加、删除DOM



```js
// 获取 
$().text()
$().html()

// 修改
$().text('')
$().html('')

// 修改css
$().css('key','value')
var div = $('#test-div');
div.css('color'); // 获取CSS属性
div.css('color', '#336699'); // 设置CSS属性
div.css('color', ''); // 清除CSS属性
div.hasClass('highlight'); // false， class是否包含highlight
div.addClass('highlight'); // 添加highlight这个class
div.removeClass('highlight'); // 删除highlight这个class

// 修改display属性
a.hide(); // 隐藏
a.show(); // 显示

// 获取DOM信息
// 浏览器可视窗口大小:
$(window).width(); // 800
$(window).height(); // 600

// HTML文档大小:
$(document).width(); // 800
$(document).height(); // 3500

// 某个div的大小:
var div = $('#test-div');
div.width(); // 600
div.height(); // 300
div.width(400); // 设置CSS属性 width: 400px，是否生效要看CSS是否有效
div.height('200px'); // 设置CSS属性 height: 200px，是否生效要看CSS是否有效

// 属性操作
div.attr('name', 'Hello');    // 设置属性
div.removeAttr('name');       // 移除属性
div.attr('name');             // 获取属性

// 无值属性，使用is()来判断
<input id="test-radio" type="radio" name="test" checked value="1">
var radio = $('#test-radio');
radio.is(':checked'); // true

// val()方法获取和设置对应的value属性：
$().val()
$().val('')


// 添加DOM
// 要添加的DOM节点已经存在于HTML文档中，它会首先从文档移除，然后再添加，相当于移动节点
$().append();
$().after();
、删除DOM
$().remove(); //移除本身
```

### jQuery事件

#### 事件绑定

$().on('event', fn)
 $().on('click', fn)  === $().click(fn)



```jsx
鼠标事件
click: 鼠标单击时触发；
dblclick：鼠标双击时触发；
mouseenter：鼠标进入时触发；
mouseleave：鼠标移出时触发；
mousemove：鼠标在DOM内部移动时触发；
hover：鼠标进入和退出时触发两个函数，相当于mouseenter加上mouseleave。

键盘事件
键盘事件仅作用在当前焦点的DOM上，通常是<input>和<textarea>。
keydown：键盘按下时触发；
keyup：键盘松开时触发；
keypress：按一次键后触发。

其他事件
focus：当DOM获得焦点时触发；
blur：当DOM失去焦点时触发；
change：当<input>、<select>或<textarea>的内容改变时触发；
submit：当<form>提交时触发；
ready：当页面被载入并且DOM树完成初始化后触发。仅作用于document对象
ready事件的三次简化：   
`document.on('ready',fn)` ===>  `$(document).ready(fn)` ===> `$(function () {})`
如果你遇到$(function () {...})的形式，牢记这是document对象的ready事件处理函数。
```

#### 取消绑定



```js
$().off('click', fn);  // fn只能传入绑定时的函数，不可包含定义

off('click')           // 一次性移除已绑定的click事件的所有处理函数。

// 无效的解除绑定，因此此处的fn对应的是一个新函数
a.off('click', function () {
    alert('hello!');
});
```

#### 事件触发条件



```js
// 监控改动
$().change(fn)
```

### jQuery AJAX



```js
var jqxhr = $.ajax('/api/categories', {
    dataType: 'json'
});
// 请求已经发送了
```

ajax的链式调用



```js
// ajax的链式调用
var jqxhr = $.ajax('/api/categories', {
    dataType: 'json'
}).done(function (data) {
    // 请求成功
    ajaxLog('成功, 收到的数据: ' + JSON.stringify(data));
}).fail(function (xhr, status) {
    // 请求失败
    ajaxLog('失败: ' + xhr.status + ', 原因: ' + status);
}).always(function () {
    // 必然调用
    ajaxLog('请求完成: 无论成功或失败都会调用');
});
```

辅助方法：



```js
// get方法，直接传参；获取返回的json
var jqxhr = $.get('/path/to/resource', {
    name: 'Bob Lee',
    check: 1
}).done(function (data) {
    // data已经被解析为JSON对象了
});

// post方法，数据作为body被发送
var jqxhr = $.post('/path/to/resource', {
    name: 'Bob Lee',
    check: 1
});
```

### jQuery插件扩展

给$.fn绑定函数，实现插件的代码逻辑；
 插件函数最后要return this;以支持链式调用；
 插件函数要有默认值，绑定在$.fn.<pluginName>.defaults上；
 用户在调用时可传入设定值以便覆盖默认值。



```js
// 给jQuery对象绑定一个新方法是通过扩展$.fn对象实现的。
$.fn.highlight = function (options) {
    // 合并默认值和用户设定值:
    // 使用jQuery提供的辅助方法$.extend(target, obj1, obj2, ...)，它把多个object对象的属性合并到第一个target对象中，遇到同名属性，总是使用靠后的对象的值，也就是越往后优先级越高：
    var opts = $.extend({}, $.fn.highlight.defaults, options);
    // 函数内部的this在调用时被绑定为jQuery对象，所以函数内部代码可以正常调用所有jQuery对象的方法。
    this.css('backgroundColor', opts.backgroundColor).css('color', opts.color);
    return this;
    // 添加添加return this，是因为jQuery对象支持链式操作，我们自己写的扩展方法也要能继续链式下去：
}

// 设定默认值: 把默认值放在这个函数对象当中，允许用户修改，而且比较合适
$.fn.highlight.defaults = {
    color: '#d85030',
    backgroundColor: '#fff8de'
}

// 用户使用时，一次性设定默认值；
$.fn.highlight.defaults.color = '#fff';
$.fn.highlight.defaults.backgroundColor = '#000';
```



```js
// 特定元素扩展
// 示例：给所有指向外链的超链接加上跳转提示
$.fn.external = function () {
    // return返回的each()返回结果，支持链式调用:
    return this.filter('a').each(function () {
        // 注意: each()内部的回调函数的this绑定为DOM本身!
        var a = $(this);
        var url = a.attr('href');
        if (url && (url.indexOf('http://')===0 || url.indexOf('https://')===0)) {
            a.attr('href', '#0')
             .removeAttr('target')
             .append(' <i class="uk-icon-external-link"></i>')
             .click(function () {
                if(confirm('你确定要前往' + url + '？')) {
                    window.open(url);
                }
            });
        }
    });
}
```

### 错误处理

#### 捕获、处理错误



```js
try {

} catch (e) {

} finally {
    
}
```

try{}包裹的代码，在执行中可能会发生错误，一旦出错，不再执行后续；跳转到catch块进行错误处理；最后无论有无错误，finally都执行。catch与finally可以只出现一个。

#### 主动抛出错误

允许抛出任意对象，包括数字、字符串。但是，最好还是抛出一个Error对象。
 `throw new Error('');`
 错误会一直向上抛，直到遇到一个有try-catch语句的函数；

### underscore第三方库，提供对Object的方法支持

使用“_”绑定全局变量；类似于jQuery的“$”



```js
_.map(obj, fn)        // 返回Array
_.mapObject(obj, fn)  // 返回Obejct

// some 与 every   类似于filter的筛选功能
_.some(obj, fn)      // obj中的至少一个元素满足条件，有一个fn返回true，_.some()就返回true
_.every(obj, fn)     // 对每一个obj的所有元素都满足条件，即每一个fn都返回true，_.every()才返回true
```

#### 分组 _.groupBy(obj, fn)



```js
var scores = [20, 81, 75, 40, 91, 59, 77, 66, 72, 88, 99];
var groups = _.groupBy(scores, function (x) {
    if (x < 60) {
        return 'C';
    } else if (x < 80) {
        return 'B';
    } else {
        return 'A';
    }
});

// 结果:
// {
//   A: [81, 91, 88, 99],
//   B: [75, 77, 66, 72],
//   C: [20, 40, 59]
// }
```

#### 打乱shuffle与随机取sample



```js
_.shuffle(obj)
_.shuffle([1, 2, 3, 4, 5, 6]); // [3, 5, 4, 6, 2, 1]

_.sample(obj)
// 随机选1个：
_.sample([1, 2, 3, 4, 5, 6]); // 2
// 随机选3个：
_.sample([1, 2, 3, 4, 5, 6], 3); // [6, 1, 4]
```

#### Array工具类

- _.first(arr)              //  取首
- _.last(arr)               //  取尾
- _.flatten(array)          // 多层嵌套数组 ===> 一维数组
- _.range(start, end, step) // 快速生成序列，start默认0，step默认1

zip与unzip与Object



```js
// zip 一一对应，合并
var names = ['Adam', 'Lisa', 'Bart'];
var scores = [85, 92, 59];
_.zip(names, scores);
// [['Adam', 85], ['Lisa', 92], ['Bart', 59]]


// unzip 一一拆解
var namesAndScores = [['Adam', 85], ['Lisa', 92], ['Bart', 59]];
_.unzip(namesAndScores);
// [['Adam', 'Lisa', 'Bart'], [85, 92, 59]]


// 一一对应，合并为对象
var names = ['Adam', 'Lisa', 'Bart'];
var scores = [85, 92, 59];
_.object(names, scores);
// {Adam: 85, Lisa: 92, Bart: 59}
```

### 高阶函数



```js
- _.bind(fn, newThis)   // 获取方法，传入原来方法的this才能保证方法被获取后成功调用
- _.partial(fn, constParam)        // 为一个函数创建偏函数，为fn固定第一个参数
- _.partial(fn, _, constParam)     // 为一个函数创建偏函数，_占位，为fn固定第二个参数

- _.memoize(fn)  // 缓存某个函数在某个参数下的计算结果
- _.once(fn)     // 返回一个只能被调用一次的函数
- _.delay(fn, time, param)  // 同setTimeOut
```

### Object 工具类



```php
 _.keys(obj)     // 返回一个object自身所有的key，但不包含从原型链继承下来的
 _.allKeys(obj)  // allKeys()除了object自身的key，还包含从原型链继承下来的：
 _.values(obj)   // 返回object自身但不包含原型链继承的所有值
 _.invert(obj)   // 把object的每个key-value来个交换，key变成value，value变成key
 _.extend(obj,obj,obj)  //把多个object的key-value合并到第一个object并返回
 _.extendOwn()    // 和extend()类似，但获取属性时忽略从原型链继承下来的属性
 _.clone()        // 复制obj，就不需要遍历每个key了
 _.isEqual()      // 内容层面的深度比较
```

### 像jQuery一样链式调用  .chain()



```tsx
// 非链式调用
_.filter(_.map([1, 4, 9, 16, 25], Math.sqrt), x => x % 2 === 1);   // [1, 3, 5]


// 通过_.chain()实现链式调用
_.chain([1, 4, 9, 16, 25])
 .map(Math.sqrt)
 .filter(x => x % 2 === 1)
 .value();                 // [1, 3, 5]
```