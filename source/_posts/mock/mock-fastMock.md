---
title: Mock.js语法
date: 2022-07-01 10:28:22
tags: mock
comment: 'valine'
categories: 
- mock

---


# Mock.js语法

### 1.基础随机内容的生成

```json
{
  "string|1-10": "=", // 随机生成1到10个等号
  "string2|3": "=", // 随机生成2个或者三个等号
  "number|+1": 0, // 从0开始自增
  "number2|1-00.1-3": 1, // 生成一个小数，小数点前面1到10，小数点后1到3位
  "boolean": "@boolean( 1, 2, true )", // 生成boolean值 三个参数，1表示第三个参数true出现的概率，2表示false出现的概率
  "name": "@cname", // 随机生成中文姓名
  "firstname": "@cfirst", // 随机生成中文姓
  "int": "@integer(1, 10)", // 随机生成1-10的整数
  "float": "@float(1,2,3,4)", // 随机生成浮点数，四个参数分别为，整数部分的最大最小值和小数部分的最大最小值
  "range": "@range(1,100,10)", // 随机生成整数数组，三个参数为，最大最小值和加的步长
  "natural": "@natural(60, 100)", // 随机生成自然数（大于零的数）
  "email": "@email", // 邮箱
  "ip": "@ip" ,// ip
  "datatime": "@date('yy-MM-dd hh:mm:ss')" // 随机生成指定格式的时间
  // ......
}
```

### 2.列表数据

```json
{
  "code": "0000",
  "data": {
    "pageNo": "@integer(1, 100)",
    "totalRecord": "@integer(100, 1000)",
    "pageSize": 10,
    "list|10": [{
      "id|+1": 1,
      "name": "@cword(10)",
      "title": "@cword(20)",
      "descript": "@csentence(20,50)",
      "price": "@float(10,100,10,100)"
    }]
  },
  "desc": "成功"
}
```

### 3.图片

mockjs可以生成任意大小，任意颜色块，且用文字填充内容的图片，使我们不用到处找图片资源就能轻松实现图片的模拟展示

```json
"code": "0000",
  "data": {
    "pageNo": "@integer(1, 100)",
    "totalRecord": "@integer(100, 1000)",
    "pageSize": 10,
    "list|10": [{
      // 参数从左到右依次为，图片尺寸，背景色，前景色（及文字颜色）,图片格式，图片中间的填充文字内容
      "image": "@image('200x100', '#ffcc33', '#FFF', 'png', 'Fast Mock')" 
    }]
  },
  "desc": "成功"
```

### 4.fastMock的跟地址

```js
//https://www.fastmock.site/mock/72e7ab90b2647c478924e7c328542c82/api
```

