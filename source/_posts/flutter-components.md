---
title: Flutter组件和路由
date: 2023-12-08 10:30:23
tags: 组件
comment: 'valine'
categories: 
- Flutter
---

# flutter 组件和路由

### 1.路由
实际的项目，是有多个不同的页面的，页面之间的跳转，就要用到路由了。 我们增加一个list页面，点击Home页的“Click Me”按钮，跳转到列表页list。

#### 1.1 单个页面的跳转
增加list.dart

```dart
import 'package:flutter/material.dart';

class ListPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    //定义列表widget的list
    List<Widget> list=<Widget>[];

    //Demo数据定义
    var data=[
      {"id":1,"title":"测试数据AAA","subtitle":"ASDFASDFASDF"},
      {"id":2,"title":"测试数据bbb","subtitle":"ASDFASDFASDF"},
      {"id":3,"title":"测试数据ccc","subtitle":"ASDFASDFASDF"},
      {"id":4,"title":"测试数据eee","subtitle":"ASDFASDFASDF"},
    ];

    //根据Demo数据，构造列表ListTile组件list
    for (var item in data) {
      print(item["title"]);

      list.add( ListTile( 
          title: Text(item["title"],style: TextStyle(fontSize: 18.0) ),
          subtitle: Text(item["subtitle"]),
          leading:  Icon( Icons.fastfood, color:Colors.orange ),
          trailing: Icon(Icons.keyboard_arrow_right)
      ));
    }

    //返回整个页面
    return Scaffold(
      appBar: AppBar(
        title: Text("List Page"),
      ),
      body: Center(
        child: ListView(
          children: list,
        )
      ),
    );
  }
}
```
在main.dart增加list页面的引入

import 'list.dart';

修改Home页的按钮事件，增加Navigator.push跳转

```dart
  FlatButton(
    color: Colors.blue,textColor: Colors.white,
    onPressed: () {    
        Navigator.push(context, MaterialPageRoute(builder:(context) {
          return  ListPage();
        }));
      },
      child: Text("Click ME",style: TextStyle(fontSize: 20.0) ),
  )
```
核心方法就是： Navigator.push(context,MaterialPageRoute)

跳转示例：

#### 1.2 更多页面跳转使用路由表
在MaterialApp中，有一个属性是routes，我们可以对路由进行命名，这样跳转的时候，只需要使用对应的路由名字即可，如： Navigator.pushNamed(context, RouterName) 。点击两个不同的按钮，分别跳转到ListPage，和Page2去。

Main.dart修改一下如下：
```dart
import 'package:flutter/material.dart';
import 'list.dart';
import 'page2.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      //路由表定义
      routes:{
        "ListPage":(context)=> ListPage(),
        "Page2":(context)=> Page2(),
      },
      home: MyHomePage(),
    );
  }
}

class MyHomePage extends StatefulWidget{
  @override
  MyHomePageState createState() => MyHomePageState();
}

class MyHomePageState extends State<MyHomePage>{
   @override
   Widget build(BuildContext context) {
       return Scaffold(
          appBar: AppBar(
            title: Text("我是Title"),
          ),
          body: Center(
            child:Column(
              children:<Widget>[
                RaisedButton(
                  child: Text("Clikc to ListPage" ),
                  onPressed: () {
                    //根据命名路由做跳转
                    Navigator.pushNamed(context, "ListPage");
                  },
                ),
                RaisedButton(
                  child: Text("Click to Page2" ),
                  onPressed: () {
                    //根据命名路由做跳转
                    Navigator.pushNamed(context, "Page2");
                  },
                )
              ]
            )
          )
      );
  }  

}
```
示例：

当我们有了路由以后，就可以开始在一个项目里用不同的页面，去学习不同的功能了。

#### 1.3 路由传参
列表页跳转到详情页，需要路由传参，这个在flutter体系里，又是怎么做的呢？

首先，在main.dart里，增加详情页DedailPage的路由配置
```dart
//路由表定义
  routes:{
    "ListPage":(context)=> ListPage(),
    "Page2":(context)=> Page2(),
    "DetailPage":(context)=> DetailPage(), //增加详情页的路由配置
  },
```
并修改ListPage里ListTile的点击事件，增加路由跳转传参，这里是将整个item数据对象传递
```dart
  ListTile( 
    title: Text(item["title"],style: TextStyle(fontSize: 18.0) ),
    subtitle: Text(item["subtitle"]),
    leading:  Icon( Icons.fastfood, color:Colors.orange ),
    trailing: Icon(Icons.keyboard_arrow_right),
    onTap:(){
      //点击的时候，进行路由跳转传参
        Navigator.pushNamed(context, "DetailPage", arguments:item);
    },
  )
```
详情页DetailPage里，获取传参并显示
```dart
import 'package:flutter/material.dart';
class DetailPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
     //获取路由传参
     final Map args = ModalRoute.of(context).settings.arguments;

    return Scaffold(
      appBar: AppBar(
        title: Text("Detail Page"),
      ),
      body: 
        new Column(
          children: <Widget>[
             Text("我是Detail页面"),
             Text("id:${args['id']}" ),
             Text("id:${args['title']}"),
             Text("id:${args['subtitle']}")
          ],
        )
      );
  }
}
```
Demo效果：


### 2.widget
Flutter提供了很多默认的组件，而每个组件的都继承自widget 。 在Flutter眼里： 一切都是widget 。 这句看起来是不是很熟悉？ 还记得在webpack里，一切都是module吗？ 类似的还有java的一切都是对象。貌似任何一个技术，最后都是用哲学作为指导思想。

widget，作为可视化的UI组件，包含了显示UI、功能交互两部分。大的widget，也可以由多个小的widget组合而成。

常用的widget组件：

#### 2.1 Text
Demo:
```dart
  Text(
    "Hello world",
    style: TextStyle(
        fontSize: 50,
        fontWeight: FontWeight.bold,
        color:Color(0xFF0000ff)
      )
    ),
```
Text的样式，来自另一个widget：TextStyle。 而TextStyle里的color，又是另一个widget Color的实例。

如果用flutter的缩进的方法，看起来确实有点丑陋，习惯写CSS的前端同学，可以看看下面的风格：

Text( "Hello world", style: TextStyle( fontSize: 50,fontWeight: FontWeight.bold,color:Color(0xFF0000ff) ) )
写成一行，是不是就顺眼多了？这算前端恶习吗？ _



#### 2.2 Button
对于flutter来说，Button就提供了很多种，我们来看看他们的区别：

RaisedButton: 凸起的按钮

FlatButton：扁平化按钮

OutlineButton：带边框按钮

IconButton：带图标按钮

按钮测试页dart:
```dart
import 'package:flutter/material.dart';

class ButtonPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {

    return Scaffold(
      appBar: AppBar(
        title: Text("Button Page"),
      ),
      body: Column(
        children: <Widget>[
          RaisedButton(
              child: Text("我是 RaiseButton" ),
              onPressed: () {},
          ),
            FlatButton(
              child: Text("我是 FlatButton" ),
              color: Colors.blue,
              onPressed: () {},
          ),
          OutlineButton(
              child: Text("我是 OutlineButton" ),
              textColor: Colors.blue,
              onPressed: () {},
          ),
          IconButton(
              icon: Icon(Icons.add),
              onPressed: () {},
          )  
        ]
      )
    );
  }
}
```
Demo:

项目中要用哪个，就各取所需吧~

#### 2.3 Container
Container是非常常用的一个widget，他一般是用作一个容器。我们先来看看他的基础属性，顺便可以想想他像HTML里的啥？

基础属性：width，height，color，child
```dart
  body: Center(
    child: Container(
        color: Colors.blue,
        width: 200,
        height: 200,
        child: Text("Hello Container ",style:TextStyle(fontSize: 20,color: Colors.white)),
    )
  )
```

Padding

我们也可以不设置宽高，用 padding 在内部撑开增加留白：
```dart
  Container(
    color: Colors.blue,
    padding: EdgeInsets.all(30),
    child: Text("Hello Container ",style:TextStyle(fontSize: 20,color: Colors.white)),
  )
```

Margin

我们还可以使用 margin ，在容器的外部撑开增加偏移量，
```dart
  Container(
    color: Colors.blue,
    padding: EdgeInsets.all(30),
    margin: EdgeInsets.only(left: 150,top: 0,right: 0,bottom: 0),
    child: Text("Hello Container ",style:TextStyle(fontSize: 20,color: Colors.white)),
  )
```

Transform

我们还可以给这个矩形，使用tansform做一些变化，比如，旋转一个角度

```dart
  Container(
    color: Colors.blue,
    padding: EdgeInsets.all(30),
    child: Text("Hello Container ",style:TextStyle(fontSize: 20,color: Colors.white)),
    transform: Matrix4.rotationZ(0.5)
  )
```

看到这里，好多前端同学要说了，好熟悉啊。 对，他就是很像Html里的一个东西： DIV ，你确实可以对应的去加强理解。

#### 2.4 Image
##### 2.4.1 网络图片加载

使用NetworkImage，可以做网络图片的加载：
```dart
  child:Image(
    image: NetworkImage("https://mat1.gtimg.com/pingjs/ext2020/qqindex2018/dist/img/qq_logo_2x.png"),
    width: 200.0,
  )
```

##### 2.4.2 本地图片加载

加载本地图片，就稍微复杂一些，首先要把图片的路径配置，加入到之前说过的pubspec.yaml配置文件里去:

image

加载本地图片时使用AssetImage：
```dart
  child:Image(
    image: AssetImage("assets/images/logo.png"),
    width: 200.0,
  )
```
也可以使用简写：
```dart
  Image.asset("assets/images/logo.png",width:200.0)
```
flutter提供的组件很多，这里就不一一举例说明，有兴趣的还是建议大家去看API： https://api.flutter.dev/

### 3.布局
我们已经了解了这么多组件，那么怎么绘制一个完整的页面呢？ 这就到了页面布局的部分了。

#### 3.1 Row & Column & Center 行列轴布局
字面意义也很好理解，行布局、列布局、居中布局，这些布局对于Flutter来说，也都是一个个的widget。

区别在于，row、column 是有多个children的widget， 而Center是只有 1个child的 widget。
```dart
  Row(
    children:<Widget>[]
  ) 

  Column(
    children:<Widget>[]
  )    

  Center(
    child:Text("Hello")
  ）
```
#### 3.2 Align 角定位布局
我们常常在Container里，需要显示的内容在左上角，左下角，右上角，右下角。 在html时代，使用CSS可以很容易的实现，但是flutter里，必须依赖Align 这个定位的Widget

右下角定位示例：
```dart
  child: Container(
    color: Colors.blue,
    width: 300,
    height: 200,
    child: Align(
      alignment: Alignment.bottomRight,
      child:Text("Hello Align ",style:TextStyle(fontSize: 20,color: Colors.white)),
    )
  )
```
显示效果：



Alignment提供了多种定位供选择，还算是很贴心的。



#### 3.3 Stack & Positioned 绝对定位
当然还有绝对定位的需求，这在css里，使用position：absolute就搞定了，但是在flutter里，需要借助stack+ positioned两个widget一起组合使用。

Stack: 支持元素堆叠

Positioned：支持绝对定位
```dart
  child:Stack(
    children: <Widget>[
        Image.network("https://ossweb-img.qq.com/upload/adw/image/20191022/627bdf586e0de8a29d5a48b86700a790.jpeg"),
        Positioned(
          top: 20,
          right: 10,
          child:Image.asset("assets/images/logo.png",width:200.0)
        )
    ],
  )
```

#### 3.4 Flex & Expanded 流式布局
Flex流式布局作为前端同学都熟悉，之前讲过的Row，Column，其实都是继承自Flex，也属于流式布局。

如果轴向不确定，使用Flex，通过修改 direction的值设定轴向
如果轴向已确定，使用Row，Column，布局更简洁，更有语义化

Flex测试页：
```dart
class FlexPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {

    return Scaffold(
      appBar: AppBar(
        title: Text("Flex Page"),
      ),
      body:  Flex(
          direction: Axis.horizontal,
          children: <Widget>[
            Container(
              width: 30,
              height: 100,
              color: Colors.blue,
            ),
            Expanded(
              flex: 1,
              child: Container(
                height: 100.0,
                color: Colors.red,
              ),
            ),
            Expanded(
              flex: 1,
              child: Container(
                height: 100.0,
                color: Colors.green,
              ),
            ),
          ],
        ),
    );
  }
}
```
示例中，轴向横向排列，最左边一个固定宽度的Container，右边两个Expanded，各自占剩下的宽度的一半。



### 4.动画
Flutter既然说了，一切都是Widget，包括动画实现，也是一个Widget。 我们还是看一个示例

#### 4.1 简单动画：淡入淡出：
使用flutter提供的现成的Widget：
```dart
import 'package:flutter/material.dart';

class AnimatePage extends StatefulWidget {
  _AnimatePage  createState()=> _AnimatePage();
} 

class _AnimatePage extends State<AnimatePage> {
  bool _visible=true;
  @override
  Widget build(BuildContext context) {

    return Scaffold(
      appBar: AppBar(
        title: Text("Animate Page"),
      ),
      body: 
        Center(
          child: Column(
            children: <Widget>[
              AnimatedOpacity(
                opacity: _visible ? 1.0:0.0,
                duration: Duration(milliseconds: 1000),
                child: Image.asset("assets/images/logo.png"),
              ),
              RaisedButton(
                child: Text("显示隐藏"),
                onPressed: (){
                  setState(() {
                    _visible=!_visible;
                  });
                },
              ),
            ],
          ),
        )    
    );
  }
}
```
其中的AnimatedOpacity就是动画透明度变化的的Widget，而被透明度控制变化的Image则是AnimatedOpacity的子元素。这个和以往前端写动画的方式，就完全不一样了，需要改变一下思维方式。

Demo效果



#### 4.2 复杂一些的动画：放大缩小
当写复杂一些动画的时候，没有对应的widget组件，就需要自己使用Animation，和AnimationController，以及Tween来组合。

Animation: 保存动画的值和状态

AnimationController: 控制动画，包含：启动forward()、停止stop()、反向播放reverse()等方法

Tween: 提供begin，end作为动画变化的取值范围

Curve：设置动画使用曲线变化，如非匀速动画，先加速，后减速等的设定。

动画示例：
```dart
class AnimatePage2 extends StatefulWidget {
  _AnimatePage  createState()=> _AnimatePage();
} 

class _AnimatePage extends State<AnimatePage2>  with SingleTickerProviderStateMixin {

  Animation<double> animation;
  AnimationController controller;

  initState() {
    super.initState();
    controller =  AnimationController(duration:  Duration(seconds: 3), vsync: this);

     //使用弹性曲线，数据变化从0到300
     animation = CurvedAnimation(parent: controller, curve: Curves.bounceIn);
     animation = Tween(begin: 0.0, end: 300.0).animate(animation)
      ..addListener(() {
        setState(() {
        });
      });

    //启动动画(正向执行)
    controller.forward();
  }

  @override
  Widget build(BuildContext context) {

    return Scaffold(
      appBar: AppBar(
        title: Text("Animate Page"),
      ),
      body: 
        Center(
          child: Image.asset(
            "assets/images/logo.png",
            width: animation.value, 
            height: animation.value
          ),
        )  
      );   
  }

  dispose() {
    //路由销毁时需要释放动画资源
    controller.dispose();
    super.dispose();
  }

}
```
很重要的一点， 在路由销毁的时候，需要释放动画资源，否则容易导致内存泄漏 。


### 5.http请求
做业务逻辑，总离不开http请求，接下来，就来看下flutter的http请求是如何做的。

#### 5.1 HttpClient
httpClient在 dart:io库中，不需要引入第三方库就可以使用，示例代码如下：

```dart
import 'dart:convert';
import 'dart:io';

Future _getByHttpClient() async{
    //接口地址
    const url="https://www.demo.com/api";

    //定义httpClient
    HttpClient client = new HttpClient();
    //定义request
    HttpClientRequest request = await client.getUrl(Uri.parse(url));
    //定义reponse
    HttpClientResponse response = await request.close();
    //respinse返回的数据，是字符串
    String responseBody = await response.transform(utf8.decoder).join();
    //关闭httpClient
    client.close();
    //字符串需要转化为JSON
    var json= jsonDecode(responseBody);
    return json;

}
```
总的看起来，代码还是挺繁琐的，使用起来并不方便。

#### 5.2 http
这是Dart.dev提供的第三方类库，地址： https://pub.dev/packages/http

需要先在pubspec.yaml里添加类库应用

dependencies:
  flutter:
    sdk: flutter
  json_annotation: ^2.0.0
  http: ^0.12.0+2
使用示例：
```dart
Future _getByDartHttp() async {
  // 接口地址
 const url="https://www.demo.com/api";//获取接口的返回值
 final response = await http.get(url);
 //接口的返回值转化为JSON
 var json = jsonDecode(response.body); 
 return json;
}
```
这种写法，比上面的httpClient简洁了许多。

Dio
国内使用最广泛的，还是flutterchina在github上提供的Dio第三方库，目前Star达到了5800多个。

官网地址： https://github.com/flutterchina/dio

使用Dio，因为是第三方库，所以同样要先在 pubspec.yaml 添加第三方库引用。

dependencies:
  flutter:
    sdk: flutter
  json_annotation: ^2.0.0
  dio: 2.1.16
使用示例：
```dart
import 'package:dio/dio.dart';

Future _getByDio() async{

      // 接口地址
      const url="https://www.demo.com/api";

      //定义 Dio实例
      Dio dio = new Dio();
      //获取dio返回的Response
      Response response = await dio.get(url);
      //返回值转化为JSON
      var json=jsonDecode(response.data);
      return json;
}
```
接口调用也是比httpclient简单很多，可能由于fluterchina在他的官方教程里，极力推荐这个dio库，所以目前这个第三方库的使用情况最为广泛。和Dart.dev的http不同的是，他需要new一个Dio的实例，在创建实例的时候，还可以传入更多的扩展配置参数。

```dart
BaseOptions options = new BaseOptions(
    baseUrl: "https://www.xx.com/api",
    connectTimeout: 5000,
    receiveTimeout: 3000,
);
Dio dio = new Dio(options);
```

### 6.缺点
学习Flutter的过程中，其实还是有很多坎坷和需要吐槽的地方。

#### 6.1 墙
因为有墙在，所以在配置flutter，或者下载flutter插件和第三方库的时候，需要墙内外来回切换。

#### 6.2 组件过度设计
提供的各种widget组件很多，但是真正核心的组件、常用的组件，也就哪些。 比如Flex 和column、row的关系，比如，Tween 与IntTween，ColorTween，SizeTween等20多个Tween子类之间的关系，你需要花很大的精力，去看每个具体子类的实现差别。

#### 6.3 嵌套太多不适应
因为嵌套层级很多，而且布局、动画、功能都在一起，第一次上手Flutter和Dart，这种嵌套关系让人很晕菜，这个只能去慢慢克服。 另外，多开发自定义的组件，可以让嵌套关系看起来清晰一些。

#### 6.4 布局修改会导致嵌套关系修改
前端的html+css分离世界里，不改变嵌套关系，修改CSS就可以调整布局。 但是在Flutter里因为布局也是嵌套关系，这就导致必须去改变嵌套关系。 要让嵌套更简单变动影响更小，页面拆分成子组件变得尤为重要。

#### 6.5 Dart语言升级
没错，语言升级也会导致学习的困扰，外面的资料新旧都有，比如有些是 new Text() ,有些直接是Text() ，新手上路会很晕菜。 其实这都是Dart语言升级导致的，记住Dart升级2.X以后，都不使用new了。感兴趣的可以自己去看下Dart的升级变更说明。

#### 6.6 不能热更新
年中的时候，Google官方宣布flutter暂不官方支持热更新，但是闲鱼团队已经有了自己的热更新方案。 关于热更新，只能静观其变了。 性能、开发效率、热更新，总是要有取舍的。即使是闲鱼团队，热更新也是付出了一点点性能下降的代价的，这是你选择flutter的初衷吗？还是那句话：权衡得失