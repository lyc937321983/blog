---
title: Flutter json 序列化
date: 2026-01-23 14:10:02
tags: json 序列化
comment: 'valine'
categories: 
- Flutter
---

## 1. json_serializable 库

### 1.1. 添加依赖

它由三个部分组成：

- **json_annotation** → 定义注解。
- **json_serializable** → 使用这些注解来生成代码。
- **build_runner** → 执行生成代码的任务。

添加库依赖的方式二选一：

```bash
# 方式一：终端直接键入下述命令安装
flutter pub add json_annotation dev:build_runner dev:json_serializable

# 方式2：打开 build.yaml文件手动添加依赖
dependencies:
flutter:
  sdk: flutter
json_annotation: ^4.8.1

dev_dependencies:
flutter_test:
  sdk: flutter
build_runner: ^2.4.7
json_serializable: ^6.7.1
```

### 1.2. 基本使用

定义Model类添加属性，给类加上 **@JsonSerializable** 注解，并添加 **fromJson()** 和 **toJson()** 方法：

```dart
import 'package:json_annotation/json_annotation.dart';

part 'banner.g.dart'; // 1.指定生成的文件，一般是当前文件.g.dart

@JsonSerializable() // 2.添加注解，告知此类是要生成Model类的
class Banner {
  @JsonKey(name: 'id') // 3.可选，添加注解，告知此属性对应的json key
  final int bid;
  final String desc;
  final String imagePath;
  final int isVisible;
  final int order;
  final String title;
  final int type;
  final String url;

  Banner({
    required this.bid,
    required this.desc,
    required this.imagePath,
    required this.isVisible,
    required this.order,
    required this.title,
    required this.type,
    required this.url,
  });

  // 4、反序列化，固定写法：_${类名}FromJson(json)
  factory Banner.fromJson(Map<String, dynamic> json) => _$BannerFromJson(json);

  // 5、序列化，固定写法：_${类名}ToJson(this)
  Map<String, dynamic> toJson() => _$BannerToJson(this);
}
```

编写完上述代码，编译器会报_BannerFromJson和_BannerToJson找不到，没关系，只要确定没拼写错误就行，直接执行下述命令生成对应的序列化代码：

```bash
flutter pub run build_runner build --delete-conflicting-outputs

# 后面的 --delete-conflicting-outputs 是可选的，作用是：
# 自动删除任何现存的，与即将生成的输出文件冲突的文件，然后继续构建过程。
# 这样可以清理由于老版本或不同构建配置造成的遗留文件
```

命令执行完，原先的报错就消失了，而且会在 **同级目录** 生成一个 **xxx.g.dart** 的文件.


然后跟前面一样调用fromJson()就行了，这里用到了这两个 **注解**，详细讲讲，加⭐的属性是比较常用的~

#### 1.2.1. @JsonSerializable

**用于**：指示生成器如何 **为类生成序/反序列化代码**，可选属性如下：

-   **explicitToJson** → 默认为false，涉及Model类嵌套时，赋值一个 **引用类型**，而不是 **显式调用嵌套类的toJson()** ！涉及对象嵌套时，建议设置为true。
-   **ignoreUnannotated** → 默认为false，如果设置为true时，生成器只序列化和反序列化用 **@JsonKey标记** 的字段。
-   **includeIfNull** → 序列化时是否包含值为null的字段，默认为true，即忽略为null的字段。
-   **genericArgumentFactories** → 用于 **泛型类的序列化和反序列化**，默认为false，如果设置为true，生成的fromJson()和toJson()将 **需要额外的类型参数的工厂函数**，以保证泛型类的正确序列化和反序列化。
- **anyMap** → 默认false，如果设置为true，生成的 fromJson() 方法将接受如何类型为Map的对象，而不仅仅是Map<String, dynamic>。
- **checked** → 默认为false，如果设置为true，生成的代码会包含对每个字段的类型检查，确保在反序列化期间的类型匹配，如果类型不匹配，会抛出一个有用的错误信息。
- **constructor** → 指定用于生成 fromJson() 工厂构造函数的名称，默认为空字符串，即使用无名构造函数。
- **createFieldMap** → 默认为true，生成器将为 **Map类型的字段** 生成额外的序列化逻辑。
- **createFactory** → 默认为true，当你需要自定义反序列化逻辑时，可以设置为false，生成器不会生成fromJson()。
- **createToJson** → 默认为true，需要自定义序列化逻辑时，可以设置为false，生成器不会生成 toJson()。
- **disallowUnrecognizedKeys** → 默认为false，设置为true时，如果输入的Json中包含Model中未定义的Key，fromJson() 将抛出一个异常。
- **fieldRename** → 控制如何将类字段的名称更改为Json键名称，枚举类FieldRename，可选值有：none(默认，不更改)、kebab(短横线命名a-b)、snak(蛇形命名a_b)、pascal(帕斯卡命名AxxBxx)。
- **converters** → 允许自定义转换器，这些转换器可以在序列化和反序列期间使用。
- **createPerFieldToJson** → 默认为false，是否为每个字段创建一个单独的 **_$[FieldName]ToJson()** 函数，当你需要对某些字段进行特殊处理时，如：自定义类型需要特殊的序列化逻辑、想根据字段的值改变输出的Json结构等，即复杂的自定义序列化，再考虑是否将这个属性设置为true，毕竟会增加代码的复杂性！

#### 1.2.2. @JsonKey

用于：定制 **单个字段的序/反序列化行为**，可选属性如下：

-   **disallowNullValue** → 如果设为true，序列化时字段为null，会抛出一个异常，通常用于确保某些字段在序列化时不为null的场景。
-   **ignore** → 如果设置为true，序列化和反序列化时会忽略这个字段。
-   **includeIfNull** → 如果设置为true，即便字段的值为null，仍然会被包含在序列化的Json中。
- **name** → 用于对Json中Key指定一个不同于Dart字段名的名称。
- **defaultValue** → json中缺少这个字段或值为null时，反序列化过程中使用的默认值。
- **fromJson** → 允许为字段提供一个自定义的反序列化函数。
- **required** → 如果设置为 true，反序列化时，如果Json中缺少这个字段会抛出一个异常。
- **toJson** → 允许为字段提供一个自定义的序列化函数。
- **unknownEnumValue** → Json中的值无法映射到Dart枚举类型中的任何值时，可以指定一个枚举默认值。



## 2.Json 生成 Model类 的一些工具

#### 2.1. QuickType

https://app.quicktype.io/

#### 2.2. json2dart

https://caijinglong.github.io/json2dart/index_ch.html

#### 2.3 vscode插件 & 其它

**JsonToDart**