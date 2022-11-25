---
title: TypeScript-基础
date: 2022-07-12 11:20:38
tags: ts-basic
comment: 'valine'
categories: 
- ts
---

# TypeScript-基础

## 1、常用类型

### 1.1 交叉类型

交叉类型就是通过 & 符号，将多个类型合并为一个类型。

```typescript
interface T1 {
  name: string;
}

interface T2 {
  age: number;
}

type T3 = T2 & T1

const a: T3 = {
  name: 'xm',
  age: 20,
}
```

### 1.2 联合类型

联合类型就是通过 | 符号，表示一个值可以是几种类型之一。

```typescript
const a: string | number = 1
```

### 1.3 字面量类型

字面量类型就是使用一个字符串或数字或布尔类型作为变量的类型。

```typescript
// 字符串
type BtnType ='default'| 'primary'| 'ghost'| 'dashed'| 'link'| 'text';

const btn:BtnType= 'primary'

// 数字
const a: 1 = 1

// 布尔
const a: true = true
```

### 1.4 字符串模板类型

字符串模板类型就是通过 ES6 的模板字符串语法，对类型进行约束。

```typescript
type https = `https://${string}`
const a:https = `https://www.baidu.com
```

## 2、运算符

### 2.1 非空断言运算符

非空断言运算符 ! 用于强调元素是非 null 非 undefined，告诉 Typescript 该属性会被明确的赋值。

```typescript
// 你可以使用类型断言将其指定为一个更具体的类型：
const myCanvas = document.getElementById("main_canvas") as HTMLCanvasElement;

// 也可以使用尖括号语法（注意不能在 .tsx 文件内使用），是等价的：
const myCanvas = <HTMLCanvasElement>document.getElementById("main_canvas");


// 当你明确的知道这个值不可能是 null 或者 undefined 时才使用 ! 
function liveDangerously(x?: number | null) {
  console.log(x!.toFixed());
}
```

### 2.2 可选运算符

可选链运算符 ?. 用于判断左侧表达式的值是否是 null 或 undefined，如果是将停止表达式运行。

```typescript
const name = object?.name
```

### 2.3 空值合并运算符

空值合并运算符 ?? 用于判断左侧表达式的值是否是 null 或 undefined，如果不是返回右侧的值。

```typescript
const a = b ?? 10
```

## 3、操作符

### 3.1 keyof

keyof 用于获取某种类型的所有键，其返回值是联合类型。

```typescript
const person: keyof {
  name: string,
  age: number
} = 'name'   //const person: 'name' | 'age' = 'name'
```

### 3.2 typeof

typeof 用于获取对象或者函数的结构类型。

```typescript
const a2 = {
  name: 'xm',
}

type T1 = typeof a2 // { name: string }

function fn1(x: number): number {
  return x * 10
}

type T2 = typeof fn1 // (x: number) => number
```

### 3.3 in

in 用于遍历联合类型。

```typescript
const obj = {
  name: 'xm',
  age: 20
}

type T5 = {
  [P in keyof obj]: string
}

/*
{ name: string, age: string }
*/
```

### 3.4 T[K]

T[K] 用于访问索引，得到索引对应的值的联合类型。

```typescript
interface I3 {
  name: string,
  age: number
}

type T6 = I3[keyof I3] // string | number
```

## 4、类型别名

类型别名用来给一个类型起个新名字。类型别名常用于联合类型。这里需要注意的是我们仅仅是给类型取了一个新的名字，并不是创建了一个新的类型

```typescript
type Message = string | string[]
let greet = (message: Message) => {
  // ...
}
```

## 5、类型断言

类型断言就是告诉浏览器我非常确定的类型。

```typescript
// 尖括号 语法
let someValue: any = "this is a string";
let strLength: number = (<string>someValue).length;

// as 语法
let someValue: any = "this is a string";
let strLength: number = (someValue as string).length;
```

## 6、类型缩小

我们可以通过某些操作将变量的类型由一个较为宽泛的集合缩小到相对较小、较明确的集合

```typescript
 let func = (anything: string | number) => {
    if (typeof anything === 'string') {
      return anything; // 类型是 string 
    } else {
      return anything; // 类型是 number
    }
  };
```

## 7、泛型

泛型就是通过给类型传参，得到一个更加通用的类型，就像给函数传参一样。 如下我们得到一个通用的泛型类型 T1，通过传参，可以得到 T2 类型 string[]、T3 类型 number[]； T 是变量，我们可以用任意其他变量名代替他。

```typescript
type T1<T> = T[]

type T2 = T1<string> // string[]

type T3 = T1<number> // number[]
```

### 7.1 常见泛型定义

- T：表示第一个参数
- K： 表示对象的键类型
- V：表示对象的值类型
- E：表示元素类型

### 7.2 泛型接口

泛型接口和上述示例类似，为接口类型传参：

```typescript
interface I1<T, U> {
  name: T;
  age: U;
}

type I2 = I1<string, number>
```

### 7.3 泛型约束

Typescript 通过 extends 实现类型约束。让传入值满足特定条件；

```typescript
interface IWithLength {
  length:number
}

function echoWithLength<T extends IWithLength>(arg:T):T{
  console.log(arg.length)
  return arg
}

echoWithLength('str')
```

通过 extends 约束了 K 必须是 T 的 key。

```typescript
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
    return obj[key];
}

let tsInfo = {
    name: "Typescript",
    supersetOf: "Javascript",
    difficulty: Difficulty.Intermediate
}

let difficulty: Difficulty =
    getProperty(tsInfo, 'difficulty'); // OK

let supersetOf: string =
    getProperty(tsInfo, 'superset_of'); // Error
```

### 7.4 泛型参数默认值

泛型参数默认值，和函数参数默认值一样，在没有传参时，给定默认值。

```typescript
interface I4<T = string> {
  name: T;
}

const S1: I4 = { name: '123' } // 默认 name: string类型
const S2: I4<number> = { name: 123 }
```

### 7.5 泛型工具类型

#### 7.5.1 typeof

typeof 的主要用途是在类型上下文中获取变量或者属性的类型；

```typescript
interface Person {
  name: string;
  age: number;
}
const sem: Person = { name: "semlinker", age: 30 };
type Sem = typeof sem; // type Sem = Person

// 使用Sem类型
const lolo: Sem = { name: "lolo", age: 5 } 
```

#### 7.5.2 keyof

keyof 可以用于获取某种类型的所有键，其返回类型是联合类型。

```typescript
interface Person {
  name: string;
  age: number;
}

type K1 = keyof Person; // "name" | "age"
type K2 = keyof Person[]; // "length" | "toString" | "pop" | "push" | "concat" | "join" 
type K3 = keyof { [x: string]: Person };  // string | number
```

#### 7.5.3 映射类型

映射类型，它是一种泛型类型，可用于把原有的对象类型映射成新的对象类型。

```typescript
interface TestInterface{
    name:string,
    age:number
}

// 我们可以通过+/-来指定添加还是删除
// 把上面定义的接口里面的属性全部变成可选
type OptionalTestInterface<T> = {
  [p in keyof T]+?:T[p]
}

// 再加上只读
type OptionalTestInterface<T> = {
  +readonly [p in keyof T]+?:T[p]
}



type newTestInterface = OptionalTestInterface<TestInterface>
```

#### 7.5.4 Partial

Partial 将类型的属性变成可选

```typescript
type Partial<T> = {
  [P in keyof T]?: T[P]
}

type T1 = Partial<{
  name: string,
}>

const a: T1 = {} // 没有name属性也不会报错
```

#### 7.5.5 Required

Required 将泛型的所有属性变为必选。

```typescript
// 语法-?，是把?可选属性减去的意思
type Required<T> = {
  [P in keyof T]-?: T[P]
}

type T2 = Required<{
  name?: string,
}>

// ts报错，类型 "{}" 中缺少属性 "name"，但类型 "Required<{ name?: string | undefined; }>" 中需要该属性。ts(2741)
const b: T2 = {}
```

#### 7.5.6 Readonly

Readonly 将泛型的所有属性变为只读。

```typescript
type T3 = Readonly<{
  name: string,
}>

const c: T3 = {
  name: 'tj',
}

c.name = 'tj1' // ts 报错，无法分配到 "name" ，因为它是只读属性。ts(2540)
```

#### 7.5.7 Pick

Pick 从类型中选择一下属性，生成一个新类型。

```typescript
// 例子一
type Pick<T, K extends keyof T> = {
  [P in K]: T[P]
}

type T4 = Pick<
  {
    name: string,
    age: number,
  },
  'name'
>

/*
这是一个新类型，T4={name: string}
*/

const d: T4 = {
  name: 'tj',
}

// 例子二
interface Todo {
  title: string;
  description: string;
  completed: boolean;
}

type TodoPreview = Pick<Todo, "title" | "completed">;

const todo: TodoPreview = {
  title: "Clean room",
  completed: false,
};
```

#### 7.5.8 Record

Record 将 key 和 value 转化为 T 类型。

```typescript
// 例子一
type Record<K extends keyof any, T> = {
  [key in K]: T
}

const e: Record<string, string> = {
  name: 'tj',
}

const f: Record<string, number> = {
  age: 11,
}


// 例子二
interface PageInfo {
  title: string;
}

type Page = "home" | "about" | "contact";

const x: Record<Page, PageInfo> = {
  about: { title: "about" },
  contact: { title: "contact" },
  home: { title: "home" },
};
```

#### 7.5.9 Exclude

Exclude 将某个类型中属于另一个的类型移除掉。

```typescript
type Exclude<T, U> = T extends U ? never : T;

type T0 = Exclude<"a" | "b" | "c", "a">; // "b" | "c"
type T1 = Exclude<"a" | "b" | "c", "a" | "b">; // "c"
type T2 = Exclude<string | number | (() => void), Function>; // string | number
```

#### 7.5.10 Extract

Extract 从 T 中提取出 U。

```typescript
type Extract<T, U> = T extends U ? T : never;

type T0 = Extract<"a" | "b" | "c", "a" | "f">; // "a"
type T1 = Extract<string | number | (() => void), Function>; // () =>void
```

#### 7.5.11 Omit

Omit 使用 T 类型中除了 K 类型的所有属性，来构造一个新的类型。

```typescript
type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>;

interface Todo {
  title: string;
  completed: boolean;
  description: string;
}

type TodoPreview = Omit<Todo, "description">;

/*
{
  title: string;
  completed: boolean;
}
*/
```

## 8、类型别名与接口区别

### 8.1 定义

接口：接口的作用就是为这些类型命名和为你的代码或第三方代码定义数据模型。

类型别名：type(类型别名)会给一个类型起个新名字。

type 有时和 interface 很像，但是可以作用于原始值（基本类型），联合类型，元组以及其它任何你需要手写的类型。

### 8.2 Objects / Functions

两者都可以用来描述对象或函数的类型，但是语法不同。

```typescript
// interface
interface Point {
  x: number;
  y: number;
}

interface SetPoint {
  (x: number, y: number): void;
}

// type
type Point = {
  x: number;
  y: number;
};

type SetPoint = (x: number, y: number) => void;
```

### 8.3 type定义其他类型

与接口不同，类型别名还可以用于其他类型，如基本类型（原始值）、联合类型、元组。

```typescript
// primitive
type Name = string;

// object
type PartialPointX = { x: number; };
type PartialPointY = { y: number; };

// union
type PartialPoint = PartialPointX | PartialPointY;

// tuple
type Data = [number, string];

// dom
let div = document.createElement('div');
type B = typeof div;
```

### 8.4 接口可以定义多次,类型别名不可以

与类型别名不同，接口可以定义多次，会被自动合并为单个接口。

```typescript
interface Point { x: number; }
interface Point { y: number; }
const point: Point = { x: 1, y: 2 };
```

### 8.5 扩展

两者都可以扩展，接口通过 extends 来实现。类型别名的扩展就是交叉类型，通过 & 来实现。

```typescript
// 接口扩展接口
interface PointX {
    x: number
}

interface Point extends PointX {
    y: number
}

// 类型别名扩展类型别名
type PointX = {
    x: number
}

type Point = PointX & {
    y: number
}

// 接口扩展类型别名
type PointX = {
    x: number
}
interface Point extends PointX {
    y: number
}

// 类型别名扩展接口
interface PointX {
    x: number
}
type Point = PointX & {
    y: number
}
```