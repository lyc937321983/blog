---
title: nest.js入门
date: 2024-08-19 19:27:22
tags: nest的第一个项目
comment: 'valine'
categories: 
- nest

---
# nest.js入门

## 一、项目创建

### 1、创建一个新的 Nest 项目

```sh
npm i -g @nestjs/cli
nest new project-name
```

将创建 `project-name` 目录，安装 node 模块和一些其他样板文件，将创建 `src/` 目录并填充几个核心文件。

以下是这些核心文件的简要概述：

| `app.controller.ts`      | 具有单一路由的基本控制器。                                   |
| ------------------------ | ------------------------------------------------------------ |
| `app.controller.spec.ts` | 控制器的单元测试。                                           |
| `app.module.ts`          | 应用的根模块。                                               |
| `app.service.ts`         | 具有单一方法的基本服务。                                     |
| `main.ts`                | 使用核心函数 `NestFactory` 创建 Nest 应用实例的应用入口文件。 |

### 2、运行应用

安装过程完成后，你可以在操作系统命令提示符下运行以下命令以启动应用监听入站 HTTP 请求：

```sh
npm run start
```

此命令启动应用，HTTP 服务器监听 `src/main.ts` 文件中定义的端口。应用运行后，打开浏览器并导航至 `http://localhost:3000/`。你应该会看到 `Hello World!` 消息

# 二、文件分析

## 1. main.ts

```js
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  await app.listen(3000);
}
bootstrap();
```

使用 `NestFactory.create()` 来创建一个 NestJS 应用程序的实例，并监听端口3000上的请求。

## 2. app.module.ts

app.module.ts是一个模块文件，以.module.ts结尾。

```js
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';

@Module({
  imports: [],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

模块是具有 `@Module()` 装饰器的类，Nest 用它来组织应用程序结构。对装饰器概念不熟悉的读者，可参考我的另一篇文章：[前端想了解后端？那得先学会 TypeScript 装饰器！](https://juejin.cn/post/7402960438861070399)。

在app.module.ts中，`@Module()` 装饰器接受一个描述模块属性的对象：

| 属性        | 说明                                                       |
| ----------- | ---------------------------------------------------------- |
| imports     | 导入子模块                                                 |
| controllers | 导入控制器                                                 |
| providers   | 由 Nest 注入器实例化的提供者，并且可以至少在整个模块中共享 |

module的内容不多，主要用来用于定义和配置模块的核心文件。

## 3. app.controller.ts

app.controller.ts是一个控制器文件，以.controller.ts结尾。

```js
import { Controller, Get } from '@nestjs/common';
import { AppService } from './app.service';

@Controller()
export class AppController {
  constructor(private readonly appService: AppService) {}

  @Get()
  getHello(): string {
    return this.appService.getHello();
  }
}
```

使用 `@Controller()` 装饰器定义一个基本的控制器，控制器负责处理传入的**请求**和向客户端返回**响应**。

构造函数使用依赖注入机制注入了 AppService 实例（在module中配置的providers: [AppService],）。private readonly 表示 appService 是私有的并且不可更改。

@Get(): 此装饰器定义了一个处理HTTP GET请求的方法。默认情况下，该方法将处理根URL（即 /）上的GET请求。因此当访问localhost:3000时，会调用getHello方法。

getHello方法调用了 AppService 的 getHello 方法。接下来继续看appService中的内容。

提示：`在controller中，不要编写过多的业务逻辑，复杂的逻辑应交给service处理`

## 4. app.service.ts

app.service.ts是一个service文件，以.service.ts结尾。主要用于处理复杂的业务逻辑。

```js
import { Injectable } from '@nestjs/common';

@Injectable()
export class AppService {
  getHello(): string {
    return 'Hello World!';
  }
}
```

使用`@Injectable()` 装饰器用于标记类为可以被依赖注入系统管理的服务，即在module中被providers。

getHello方法返回字符串'Hello World!'，因此在app.controller.ts中，调用this.appService.getHello()返回的结果是'Hello World!'，访问localhost:3000接口的结果为'Hello World!'。

# 三、创建模块

通过上文，熟悉了NestJS的设计模式，主要就是 `Controller`、`Service`、`Module` 共同形成了一个模块。

- `Controller`：传统意义上的控制器，提供 api 接口，负责处理路由、中转、验证等一些简洁的业务；
- `Service`：又称为 `Provider`， 是一系列服务、repo、工厂方法、helper 的总称，主要负责处理具体的业务，如数据库的增删改查、事务、并发等逻辑代码；
- `Module`：负责将 `Controller` 和 `Service` 连接起来，类似于 `namespace` 的概念；

使用 nest-cli 提供的指令可以快速创建文件，语法如下：

```js
nest g [文件类型] [文件名] [文件目录]
```

文件目录如果不提供，默认为文件名。

## 1. 创建模块

```sh
nest g module posts
```

执行完命令后，我们可以发现在根模块app.module.ts中的@Model装饰器的imports中自动引入了PostsModule

命令可简写为：

```shell
nest g mo posts
```

## 2. 创建控制器

--no-spec：不生成测试文件

```sh
nest g controller posts --no-spec
```

创建posts.controller.ts文件，并且在posts.module.ts文件下，@Module装饰器的controllers中自动注入

命令可简写为：

```sh
nest g co posts --no-spec
```

## 3. 创建服务类

```sh
nest g service posts --no-spec
```

创建posts.service.ts文件，并且在posts.module.ts文件下，@Module装饰器的providers中自动注入

命令可简写为：

```sh
nest g s posts --no-spec
```

提示：`注意创建顺序，先创建Module，再创建Controller和Service，这样创建出来的文件在Module中自动注册。`
