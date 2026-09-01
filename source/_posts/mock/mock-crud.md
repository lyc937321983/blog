---
title: Mock.js CRUD
date: 2022-07-01 11:50:22
tags: mock-crud
comment: 'valine'
categories: 
- mock

---

# mock.js注解实现增删改查CRUD

注:CRUD即:增Create、查Retrive、删Delete、改Update

```js
// cdn引入文件 ：
// <script src="http://mockjs.com/dist/mock.js"></script>
```



### 1、创建一个对象

```js
var arr =  [
		{
			name: 'fei',
			age: 20,
			id: 1
		},
		{
			name: 'liang',
			age: 30,
			id: 2
		}
	]
```

​	   

### 2、删除元素

```js
Mock.mock('ip地址', 'delete', function(options) {
	var id = parseInt(options.body.split("=")[1]) 
	var index;
	for (var i in arr) {
		if (arr[i].id === id) { 
			index = i
			break;
		}
	}
	arr.splice(index, 1) 
	return arr; 
})

$.ajax({
	url: 'ip地址',
	type: "DELETE",
	data: {
		id: 1 
	},
	dataType: 'json',
	success: function(e) {
		console.log(e)
	}
})
```


### 3、添加元素

```js
Mock.mock('ip地址', 'push', function(options) {
	var id = parseInt(options.body.split("=")[1]) 
	var index;
	for (var i in arr) {
		if (arr[i].id === id) { 
			index = i
			break;
		}
	}
	arr.push(index) 
	return arr; 
})

$.ajax({
	url: 'ip地址',
	type: "push",
	data: {
		id: 1 
	},
	dataType: 'json',
	success: function(e) {
		console.log(e)
	}
})
```


### 4、编辑元素

```js
Mock.mock('ip地址', 'post', function(options) {
	var id = parseInt(options.body.split("=")[1])
	for (var i in arr) {
		for (var i = 0; i < arr.length; i++) {
			if (arr[i].id === id) { 
				arr[i].name = decodeURI(options.body.split("=")[2])
				// index = i
				// break;
			}
		}
	}
	return arr; 
})

$.ajax({
	url: 'ip地址',
	type: "POST",
	data: {
		id: 2, 
		mane: 'qwe'
	},
	dataType: 'json',
	success: function(e) {
		console.log(e)
	}
})
```
### 5、查找元素

```js
Mock.mock('ip地址', 'push', function(options) {
	var id = parseInt(options.body.split("=")[1]) 
	var findo = {};
	for (var i in arr) {
		if (arr[i].id === id) {
	    	findo[i] = arr[i]
			break;
		}
	}
	return findo; 
})
		
$.ajax({
	url: 'ip地址',
	type: "push",
	data: {
		id: 3, 
	},
	dataType: 'json',
	success: function(e) {
		console.log(e)
	}
})
```
