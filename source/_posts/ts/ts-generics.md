---
title: TypeScript 的泛型
date: 2024-12-27 15:50:22
tags: TypeScript 的泛型
comment: 'valine'
categories: 
- ts

---

# TypeScript 的泛型

TypeScript 的泛型（Generics）是用于创建可复用组件的一种强大工具。它们允许你编写能够处理任意类型数据的函数、接口或类，同时保持严格的类型检查。通过使用泛型，你可以确保代码的灵活性和类型安全性。

### 泛型的基本概念

- **泛型函数**：可以接受任何类型的参数，并返回相同或不同的类型。
- **泛型类**：可以在定义类时指定一个或多个类型参数，使类中的属性和方法能够操作这些类型。
- **泛型接口**：为实现该接口的对象提供类型参数，以确保对象中特定成员的类型一致性。
- **泛型约束**：限制泛型参数的类型范围，以确保某些操作在这些类型上是有效的。

### 使用泛型的例子

#### 1. 泛型函数

最简单的泛型用法是定义一个泛型函数。这里有一个例子，展示了如何创建一个能返回输入值的函数：

```ts
function identity<T>(arg: T): T {
    return arg;
}

let output = identity<string>("myString"); // 指定T为string类型
console.log(output);
```

在这个例子中，`identity` 函数可以接受任何类型的参数，并返回相同的类型。当你调用 `identity` 函数时，可以通过尖括号 `<T>` 显式地指定类型参数，也可以让 TypeScript 自动推断类型。

#### 2. 泛型类

下面是一个泛型类的例子，它使用了一个类型参数来确保属性和方法的一致性：

```ts
class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };
console.log(myGenericNumber.add(10, 20)); // 输出30
```

#### 3. 泛型接口

你可以使用泛型来定义接口，使得接口可以适应多种类型：

```ts
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);  // 现在我们知道arg有length属性
    return arg;
}

loggingIdentity({length: 10, value: 3});
```

在这个例子中，我们定义了一个 `Lengthwise` 接口，它要求实现的对象必须拥有 `length` 属性。然后我们定义了一个泛型函数 `loggingIdentity`，它只接受实现了 `Lengthwise` 接口的对象作为参数。

#### 4. 泛型约束

有时你可能想要限制泛型的类型范围，以确保某些操作是可以执行的。这可以通过使用类型约束来实现：

```ts
function getProperty<T, K extends keyof T>(obj: T, key: K) {
    return obj[key];
}

let x = { a: 1, b: 2, c: 3, d: 4 };

getProperty(x, "a"); // ok
// getProperty(x, "m"); // 错误，'m' 不是 'x' 的键
```

在这个例子中，`K extends keyof T` 表示 `K` 必须是 `T` 对象的一个有效键名。

### 总结

泛型是 TypeScript 中非常有用的功能，它可以帮助你编写更加通用和安全的代码。通过定义泛型函数、类和接口，你可以创建灵活且类型安全的组件，适用于各种不同类型的输入。同时，泛型约束让你能够在保证灵活性的同时，确保代码只能按照预期的方式工作。