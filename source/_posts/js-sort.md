---
title: javascript sort方法排序
date: 2022-01-07 15:45:22
tags: sort()
comment: 'valine'
categories: 
- js
---

# javascript sort方法排序

### 1.字母排序

sort默认的排序方式为字母排序，根据二十六个字母依次排列，单词之间比较，则先比较第一个字母，如果第一个字母相同则比较第二个字母，以此类推。

```js
// 1.字母排序（sort默认排序）
var arr = ["za","zb","a","b","xc","xa"];
arr.sort();
console.log(arr);
// 运行结果：["a", "b", "xa", "xc", "za", "zb"]
```

### 2.数字排序

sort()中参数可以是方法函数，可以升序和降序输出结果。

```js
//2.sort数字排序
var array = [100,10,50,800,320,34,53];
array.sort(function(a,b){
    //a-b升序，b-a降序
    return b-a;
});
console.log(array);
//运行结果：[800, 320, 100, 53, 50, 34, 10]
```

**注意：**其中a,b都是表示这个数组里面的元素，如果是a-b则表示升序，如果是b-a则表示降序。

### 3.数组对象排序

最重要的还是这个对象属性排序，当后台给我们前端很多数据并且没有排序时，我们一般都是要重新进行排序，而后台给的数据往往是好几层，不会像前面那种简单的就一个数组，这个时候就要用sort中对象属性排序了

```js
// 3.对象属性排序
var obj = [
    {name:"lucy", num:400},
    {name:"nancy", num:110},
    {name:"maria", num:200}
];
obj.sort(compare("num"));
console.log(obj);

//数组对象属性值排序
function compare(property){
    return function(a,b){
        //value1 - value2升序
        //value2 - value1降序
        var value1 = a[property];
        var value2 = b[property];
        return value1 - value2;//升序
    }
}
//运行结果：
//[
// {name:"nancy", num:110},
// {name:"maria", num:200},
// {name:"lucy", num:400}
//]
```

**注意：**compare()中参数必须是这个对象的属性名称，而你要比较的这些对象里面，一定要有这个属性名称，否则会出错。

