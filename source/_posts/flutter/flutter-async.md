---
title: Flutter 异步编程API
date: 2026-01-06 10:30:23
tags: Flutter async
comment: 'valine'
categories: 
- Flutter
---

## Dart异步编程API

Dart 中的异步编程API，主要是通过 **Future** 和 **Stream** 两个API来实现的

### 1.1. Future

Dart中的 **Future** 代表 **一个异步任务**，定义一个异步任务的代码示例如下：

```dart
Future<String> fetchData() {
  // 休眠3s模拟执行耗时操作
  return Future.delayed(const Duration(seconds: 3), () {
    return "Hello Flutter!";
  });
}
```

#### 1.1.1. async + await

两个关键字提供了一种 **类似同步代码的方式来编写异步操作**：

- **async** → 用于声明一个异步函数；
- **await** → 用于等待一个异步操作的结果；

调用上述异步任务的代码示例如下：

```dart
Future<void> printData() async {
  var result = await fetchData();
  print(result);
}
```

另外，建议对异步操作中可能出现的错误进行处理，直接使用 **try-catch** 关键字进行异常捕获：

```dart
Future<void> printData() async {
    try {
      var result = await fetchData();
      print(result);
    }  on IntegerDivisionByZeroException catch (e) {
      print("除0异常");
    } catch (e) {
      print(e);
    } finally {
      print("异常与否最终都要执行的代码块");
    }
  }
```

对了，还有一点要注意：**使用await会等待，直到异步操作完成才继续往下执行代码**，比如这样的代码：

```dart
// 定义三个异步请求
Future<String> fetchUserOrder1() => Future.delayed(Duration(seconds: 1), () => 'Order 1');

Future<String> fetchUserOrder2() => Future.delayed(Duration(seconds: 2), () => 'Order 2');

Future<String> fetchUserOrder3() => Future.delayed(Duration(seconds: 3), () => 'Order 3');

Future<void> doTasks() async {
	var startTime = DateTime.now().second;
  await fetchUserOrder1();
  await fetchUserOrder2();
  await fetchUserOrder3();
  var endTime = DateTime.now().second;
  print(endTime - startTime);	// 输出：6
}
```

因为等待，所以总的运行时间为：1+2+3=6s，如果想三个请求同时执行，可以改下写法：

```dart
Future<void> doTasks() async {
  var startTime = DateTime.now().second;
  var order1 = fetchUserOrder1();
  var order2 = fetchUserOrder2();
  var order3 = fetchUserOrder3();
  await order1;
  await order2;
  await order3;
  var endTime = DateTime.now().second;
  print(endTime - startTime);	// 输出：3
}
```

也可以用下下面提到的Future.wait()方法来实现多个耗时任务并行。

#### 1.1.2. then() + catchError() + whenComplete()

这三个方法是Future的一个API，它允许你在Future **成功完成时**、**异常结束时**、**任务完成时(无论成败)** 时执行一个回调参数，使用代码示例如下：

```dart
void printData() {
  fetchData().then((result) {
    print("获取异步结果并输出：$result");
  }).catchError((error) {
    print("捕获异常：$error");
  }).whenComplete(() {
    print("无论是否捕获异常，都会执行的代码块");
  });
}
```

Future 的 then() 方法代码如下：

```dart
Future<R> then<R>(FutureOr<R> onValue(T value), {Function? onError})
```

返回一个Future，所以在处理连续请求时，可以 **连续追加多个then** 来规避回调地狱，伪代码如下：

```dart
fetchData()
    .then((value) => "写入数据库")
    .then((value) => "刷新UI")
    .then((value) => "埋点上报")
    .catchError((error, stackTrace) => print("stackTrace"));
```

### 2.2. Stream (流)

**Future** 代表 **一个异步任务**，**Stream** 则代表 **一个异步任务序列**，即 **一连串的异步任务**。你可以监听Stream来获取它的结果 (数据和错误)，也可以在Stream完成前对它进行暂停或停止监听，它有两种类型：

- **Single-subscription**：**单订阅流**，只能被一个监听器监听；
- **Broadcast**：**广播流**，能被多个监听器同时监听；

#### 2.2.1. 创建Stream的几种方法

① 使用 **Stream的构造方法创建**

- **Stream.fromFuture()：** 将一个Future转化为Stream流；
- **Stream.fromFutures()** ：将一个Future列表转换为Stream流；
- **Stream.fromIterable()** ：将一个Iterable (如List或Set) 转换成 Stream流；
- **Stream.periodic()** ：创建一个周期性发出事件的Stream流；
- **Stream.empty()** ：创建一个空的流，不包含任何事件；
- **Stream.value()** ：创建一个单一值的流，流中只有一个事件；
- **Stream.error()** ：创建一个包含错误事件的流；
- **Stream.multi()** ：允许你使用一个事件生成器函数来控制流的发送，用于创建具有复杂行为的流；

简单代码示例：

```dart
 Future<String> fetchAsyncData() async {
  await Future.delayed(const Duration(seconds: 2));
  return 'Future Fetched data';
}

void testStream() {
  Stream.fromIterable([1, 2, 3, 4, 5]).listen((event) => print(event)); // 输出：1、2、3、4、5
  Stream.periodic(const Duration(seconds: 1), (computationCount) => computationCount)
      .take(5)
      .listen((event) => print(event)); // 输出(间隔1s)：0、1、2、3、4
  Stream.fromFuture(fetchAsyncData()).listen((event) => print(event));  // 输出：Future Fetched data
}
```

② 使用 **async*** + **yield** 或 **yield*** 创建

注意这个 **async*** 是有个*星号的哈，不是 **async** ！它用于标记一个 **异步生成器函数** (返回Stream对象的函数)，可以在等待异步操作完成的同时产生多个值。然后是给 **Stream监听器传递值** 的两种方式：

- **yield**：每次调用yield时都会向Stream中添加一个值，函数执行到yield语句时会暂停，直到Stream监听器装备好接收下一个值才继续执行，这允许你构建一个可以产生多个值的函数，且这些值不是立即生成的，而是随着消费者的接收能力逐一生成。
- **yield*** ：将一个 Stream 的所有值插入到另一个 Stream 中，当生成器函数遇到yield*时，它会等待并传递所有来自另一个 Stream 的值，直到那个 Stream 完成。

简单代码示例如下：

```dart
 // 定义一个异步生成器函数，它使用yield来产生数字1到3
Stream<int> numberStream() async* {
  for (int i = 1; i <= 3; i++) {
    yield i; // 向 Stream 发送值 i
    await Future.delayed(Duration(seconds: 1)); // 模拟异步等待
  }
}

// 另一个异步生成器函数使用yield*来传递numberStream生成的所有值
Stream<int> replicatedNumberStream() async* {
  yield* numberStream(); // 将 numberStream 的所有值传递给当前 Stream
}

void main() async {
  print('Start listening to numberStream');
  await for (int number in numberStream()) {
    print('Got a number from numberStream: $number');
  }

  print('Start listening to replicatedNumberStream');
  await for (int number in replicatedNumberStream()) {
    print('Got a number from replicatedNumberStream: $number');
  }
}

// 输出结果：
// Start listening to numberStream
// Got a number from numberStream: 1
// Got a number from numberStream: 2
// Got a number from numberStream: 3
// Start listening to replicatedNumberStream
// Got a number from replicatedNumberStream: 1
// Got a number from replicatedNumberStream: 2
// Got a number from replicatedNumberStream: 3
```

③ 使用 **StreamController** 创建

这种方式创建和使用Stream流更加灵活，先明确四个角色：

- **Stream**：**数据源**，可以被监听，单订阅流只能有一个监听器，而广播流可以有多个监听器；
- **StreamController**：**Stream流的控制器**，可以在Stream上发送数据、错误和完成事件、也可以检查Stream是否暂停、是否有订阅者，以及在其它任何发生改变时获取到回调。提供了两个工厂方法来创建实例：**StreamController()** 和 **StreamController.broadcast()** ，分别对应单订阅流和广播流。
- **StreamSink**：**添加Stream事件的抽象类**，用于添加数据、错误和关闭事件到Stream上，StreamController 实现了此接口，因此它也可以作为一个StreamSink使用。
- **StreamSubscription**：**Stream的监听对象**，它可以监听Stream上的数据、错误和完成事件，也可以暂停、恢复和取消订阅。当你调用Stream的 **listen()** 方法时，会返回一个 StreamSubscription 对象，可以使用它来控制订阅。

简单代码示例如下：

```dart
import 'dart:async';

void main() {
  // 创建一个单订阅流的 StreamController
  // 如果想创建广播流可以用 StreamController.broadcast()
  var controller = StreamController<int>();

  // 订阅Stream
  controller.stream.listen(
    (data) {
      print('Received data: $data');
    },
    onDone: () {
      print('Stream is closed');
    },
    onError: (error) {
      print('Error occurred: $error');
    },
  );

  // 往Stream中添加数据
  controller.sink.add(1);
  controller.sink.add(2);
  controller.sink.addError('Something went wrong');
  controller.sink.add(3);

  // 关闭StreamController时，会向Stream发送关闭事件
  // 需要在确保不再发送数据的情况下执行此操作，以防止内存泄露和资源浪费
  controller.close();
}

// 输出结果：
// Received data: 1
// Received data: 2
// Error occurred: Something went wrong
// Received data: 3
// Stream is closed
```

没有定义StreamSubscription变量兜住listen()的返回值，然后调用cancel() 取消订阅不会**内存泄露** 吗？

> 答：不会。cancel() 方法一般在监听器不需要接收数据，但Stream还未结束时使用。当调用StreamController的close() 方法时，该控制器上的Stream会结束，一旦Stream结束，它会自动发送一个完成事件给所有监听器，并关闭Stream。这种情况下，监听器就不需要显式调用 cancel() 来取消订阅，因为Stream已经完成。

另外，构造参数中还支持传入一个bool类型的 **sync参数** (默认false) 决定是否创建一个同步类型的StreamController，即事件添加和监听处于同一个Event Loop中，不太建议设置为true，可能导致潜在的堆栈溢出错误。

### 2.3. Isolate (隔离)

在执行 **计算密集型耗时任务** 的场景，创建新的isolate来处理耗时任务，避免堵塞主isolate，任务执行完毕后通过端口通知主isolate。

先补充下这四个角色的描述吧：

- **Isolate**：**独立的Dart执行上下文**，可以通过 spwan() 或 spawnUri() 来创建一个新的 isolate；
- **ReceivePort** & **SendPort**：收发其它Isolate消息的端口，可以通过 sendPort属性获取一个SendPort对象，用于发送消息给对应的ReceiverPort；
- **Capability**：isolate的唯一标识，用于控制isolate的暂停和恢复；

再写个简单代码示例：

```dart
void doCPUTask() async {
    // 创建一个ReceivePort，用来接收来自新Isolate的消息
    final receivePort = ReceivePort();

    // 创建并启动一个新的Isolate，它将并行执行累加操作
    final isolate = await Isolate.spawn((sendPort) {
      int sum = 0;
      for (int i = 0; i < 1000000000; i++) {
        sum += i;
      }
      // 计算完毕，通过Port发送结果回主Isolate
      sendPort.send(sum);
      sendPort.send('Hello');
      sendPort.send('World');
      // 发送一个错误消息给子Isolate
      sendPort.send(Error());
      sendPort.send('exit');
    }, receivePort.sendPort);

    // 监听ReceivePort的信息
    receivePort.listen((message) {
      // 打印接收到的消息
      print('接收到消息: $message');
      // 如果接收到的消息是'exit'，则关闭ReceivePort并杀死子Isolate
      if (message == 'exit') {
        receivePort.close();
        isolate.kill();
      }
    }, onError: (error, stackTrace) {
      print("处理错误信息：$error}");
    }, onDone: () {
      print("ReceivePort 关闭");
    });
  }
```