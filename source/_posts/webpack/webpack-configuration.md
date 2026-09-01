---
title: webpack4基本配置
date: 2022-11-18 16:00:22
tags: webpackp配置
comment: 'valine'
categories: 
- webpack

---

# webpack4基本配置

## 前言

#### 1.官网解释

- 本质上，**webpack** 是一个用于现代 JavaScript 应用程序的 *静态模块打包工具*。当  webpack 处理应用程序时，它会在内部从一个或多个入口点构建一个 [依赖图(dependency graph)]，然后将你项目中所需的每一个 模块组合成一个或多个 *bundles*，它们均为静态资源，用于展示你的内容

#### 2.理解

- Webpack可以看做是模块打包机，它做的事情是分析你的项目结构，找到JavaScript模块以及其它的一些浏览器不能直接运行的拓展语言（例如：Scss，TypeScript等），并将其打包为合适的格式以供浏览器使用。

#### 3.webpack 基础概念

1. Entry：入口，Webpack 执行构建的第一步将从 Entry 开始，可抽象成输入。
2. Module：模块，在 Webpack 里一切皆模块，一个模块对应着一个文件。Webpack 会从配置的 Entry 开始递归找出所有依赖的模块。
3. Chunk：代码块，一个 Chunk 由多个模块组合而成，用于代码合并与分割。
4. Loader：模块转换器，用于把模块原内容按照需求转换成新内容。
5. Plugin：扩展插件，在 Webpack 构建流程中的特定时机注入扩展逻辑来改变构建结果或做你想要的事情。
6. Output：输出结果，在 Webpack 经过一系列处理并得出最终想要的代码后输出结果。

## 1. 从0开始配置结构

- 初始化项目结构

```shell
mkdir webpack-init
cd webpack-init
npm init -y
npm install webpack webpack-cli webpack-dev-server -g //安装webpack配置需要的包

//新建src目录，并在下边新建index.html，并引入index.js
mkdir src
cd src
touch index.html
touch index.js
```



## 2. 配置webpack.config.js

- 在项目根目录新建webpack.config.js

```js
module.exports = {
    entry:'./src/index.js',//入口文件，src下的index.js
    output:{
        publicPath:__dirname+"dist",//js引用的路径也是可以是cdn地址
        path: path.resolve(__dirname,'dist'),//出口目录，dist文件
        filename: '[name].[hash].js'//这里那么就是打包出来的文件名
    },
    module:{},
    plugins:[],//加s的都是数组
    devServer:{}
}
```



## 3. 配置开发服务器

```js
//修改wbepack.config.js 文件

devServer:{
    contentBase:path.resolve(__dirname,"dist"),
    port:9090,
    host:'localhost',
    overlay:true,
    compress:true  //服务器返回浏览器的时候是否启动gzip压缩
}

```



## 4. 打包js

```json
//修改package.json 文件
"script" : {
    "build" : "webpack --mode prodection",
    "dev" : "webpack-dev-server --open --mode development"
}

// --mode production 和 --mode devlopment 的区别就是是否进行代码的压缩
// 运行npm run build 既可打包生产环境
// 运行npm run dev 既可热加载，进行开发
```



## 5. 支持ES6,react,vue

```json
// cnpm i babel-loader @babel/core @babel/preset-env @babel/preset-react -D

{
    test: /\.jsx?$/,
    exclude:/node_modules/,
    use: [
        {
            loader: "babel-loader"
        }
    ]
}

//.babelrc文件
{
    "presets": ["@babel/preset-env","@babel/preset-react"]
}
```



## 6. 处理css,sass,以及css3属性前缀

### 6.1 处理css

```json
// npm install style-loader css-loader postcss-loader autoprefixer -D
// css-loader 用来处理css中url的路径
// style-loader可以把css文件变成style标签插入head中
// 多个loader是有顺序要求的，从右往左写，因为转换的时候是从右往左转换的

module: {
    rules: {
        test:/\.css$/,
        use: [
            {
                loader: "style-loader",
                options: {
                    singeton: true //处理为单个style标签
                }
            },
            {
                loader: "css-loader"
            },
            {
                loader: "postcss-loader"
            }
        ],
        include: path.resolve(__dirname,'dist'),//限制范围，提高打包速度
        exclude: /node_modules
    }
}

// css前缀：新建一个postcss.config.js文件，输入
module.exports = {
    plugins: [
        require('qutoprefixer')
    ]
}
```



### 6.2 动态卸载和加载`CSS`

> style-loader为 css 对象提供了use()和unuse()两种方法可以用来加载和卸载css

比如实现一个点击切换颜色的需求，修改index.js

```js
import index from "./css/index.css"
let flag =false;
$('#btn').click(function(){
	flag?index.use():index.unuse();
})
```



### 6.3 处理sass

```json
// npm install sass-loader node -sass style-loader css-loader --save-dev

//webpack.config.js
module.exports = {
    module: {
        rules: [{
            test: /\.scss$/,
            use: [{
                loader:"style-loader" //将JS字符串生成为style节点
            },{
                loader:"css-loader" //将css转化成CommonJS模块
            },{
                loader:"sass-loader" //将sass编译成css
            }]
        }]
    }
}
```



### 6.4 提取css文件为单独文件

```json
 npm install --save-dev extract-text-webpack-plugin@nest  //注意：一定是next版本的

//不再需要style-loader
rules: [
    {
        test: /\.css$/
        //
        use: ExtractTextWebpackPlugin.extract({
        	use:'css-loader'
    })
    }
]

new ExtractTextWebpackPlugin('index.css')
```





## 7. 产出html



```json
// npm i html-webpack-plugin ---save-dev
plugins:[
    new HtmlWebpackPlugin({
        template: path.resolve(__dirname,'src','index.html'),//模板
        filename: 'index.html',
        hash: true,//防止缓存
        minify: {
            removeAttributeQuotes: true  //压缩 去掉引号
        }
    })
]
```



## 8. 处理引用的第三方库,暴露全局变量

webpack.ProvidePlugin参数是键值对形式，键就是我们项目中使用的变量名，值就是键所指向的库



```json
const webpack = require("webpack");

new webpack.ProvidePlugin({
    "$":'jquery'
})
```



## 9. code splitting、懒加载(按需加载)

说白了就是在需要的时候在进行加载，比如一个场景，点击按钮才加载某个js.



```js
//  require.ensure的方式

if(page=='subPageA'){
	require.ensure(['./subPageA'],function(){
        var subPageA = require('subPageA');
    },'subPageA')
}else if(page=='subPageB'){
    require.ensure(['./subPageB'],function(){
        var subPageA = require('subPageB');
    },subPageB)
}

// import的方式
import * as _ from 'lodash'
var page = 'subpageA'
if(page==-"subpageA"){
	import(/*webpackChunkName:'subpageA'*/'./subpageA').then(function(subpageA){
		console.log("subpageA")
    })
}else if(page==-"subpageB"){
	import(/*webpackChunkName:'subpageB'*/'./subpageB').then(function(subpageB){
		console.log("subpageB")
    })
}

export default 'pageA'
```



## 10. JS Tree Shaking



```json
// 在webpack之前的版本中，这样使用，显式配置UglifyjsWebpackPlugin即可

const UglifyJSPlugin = require('uglifyjs-webpack-plugin');
plugins: [
    new UglifyJSPlugin()
]

//但是在webpack4中，只需要配置mode为"production"即可
```



## 11. 图片处理



```json
// yarn add file-loader url-loader -D
//file-loader 解决css等文件中引入图片路径的问题
//url-loader 当图片较小的时候把图片BASE64编码，大于limit参数的时候还是使用file-loader进行拷贝

{
    // file-loader是解析图片地址，把图片从源文件拷贝到目标文件并且修改源文件名称
    // 可以处理任意二进制，bootstrap里的文字
    // url-loader可以在文件比较小的时候，直接变成base64字符串内嵌到页面中
    {
    	test: /\.(png|jpg|jpeg|gif|svg),
    	use: {
    		loader: "url-loader",
    		options: {
  				outputPath: 'image/', // 图片输出的路径
    			limit: 5 * 1024
			}
		}
	}
}
```



## 12. Clean Plugin and Watch Mode

清空目录，文件有改动就重新打包

```js
new CleanWebpackPlugin(["dist"])

// webpack --watch 即可开启watch mode
```



## 13. 区分环境变量

```json
new webpack.DefinePlugin({
NODE_ENV:JSON.stringify(process.env.NODE_ENV)
})

"scripts": {
    "build": "cross-env NODE_ENV=production webpack --mode development"
}
```



## 14. 开发模式与webpack-dev-server,proxy

```json
devServer: {
    contentBase: path.resolve(__dirname,"dist"),
    port: 8000,//端口号
    hot: true,//热重载
    overlay:true,//代码出错弹出浮动层
    proxy: {
        //跨域代理转发
        "/comments": {
            target: "https://m.weibo.cn",
            changeOrigin: true,
            logLevel: "debug",
            headers: {
                Cookie: ""
            }
        }
    },
    historyApiFallback: {
        // HTML5 history模式
        rewrites:[{ from: /.*/, to: "/index.html"}]
    }
}
```

