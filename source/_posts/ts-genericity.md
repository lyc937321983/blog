---
title: TS泛型
date: 2024-01-02 17:25:22
tags: TS泛型
comment: 'valine'
categories: 
- ts

---

# TS泛型

### 一、Exclude

`Exclude<T, U>`：作用简单说就是把 `T` 里面的 `U` 去掉，再返回 `T` 里还剩下的。`T` 和 `U` 必须是同种类型(具体类型/字面量类型)。

```ts
type T1 = Exclude<string | number, string>;
// type T1  = number; 

type T2 = Exclude<'a' | 'b' | 'c', 'b' | 'd'>;
// type T2  = 'a' | 'c';
```

### 二、Extract

`Extract<T, U>`：作用是取出 `T` 里面的 `U` ，返回。作用和 `Exclude` 刚好相反，传参也是一样的

看例子理解 `Extract`

```ts
type T1 = Extract<'a' | 'b' | 'c', 'a' | 'd'>;
// type T1  = 'a';

// 源码定义
type Extract<T, U> = T extends U ? T : never
```

### 三、Omit

`Omit<T, K>`：作用是把 `T(对象类型)` 里边的 `K` 去掉，返回 `T` 里还剩下的

`Omit` 的作用和 `Exclude` 是一样的，都能做类型过滤并得到新类型。

不同的是 `Exclude` 主要是处理联合类型，且会触发分发，而 `Omit` 主要是处理对象类型，所以自然的这俩参数也不一样。

用法如下

```ts
// 这种场景 type 和 interface 是一样的，后面就不重复说明了
type User = {
    name: string
    age: number
}
type T1 = Omit<User, 'age'>
// type T1 = { name: string }
```

源码定义

```ts
// keyof any 就是 string | number | symbol
type Omit<T, K extends keyof any> = { [P in Exclude<keyof T, K>]: T[P]; }
```

- 首先第一个参数 `T` 要传对象类型， `type` 或 `interface` 都可以
- 第二个参数 `K` 限制了类型只能是 `string | number | symbol`，这一点跟 `js` 里的对象是一个意思，对象类型的属性名只支持这三种类型
- `in` 是映射类型，用来映射遍历枚举类型。大白话就是循环、循环语法，需要配合联合类型来对类型进行遍历。`in` 的右边是可遍历的枚举类型，左边是遍历出来的每一项
- 用 `Exclude` 去除掉传入的属性后，再遍历剩下的属性，生成新的类型返回

示例解析：

```ts
type User = {
    name: string
    age: number
    gender: string
}
type Omit<T, K extends keyof any> = { [P in Exclude<keyof T, K>]: T[P]; }
type T1 = Omit<User, 'age'>
// type T1 = { name: string, gender: string }
```

我们调用 `Omit` 传入的参数是正确的，所以就分析一下后面的执行逻辑：

- `Exclude<keyof T, K>` 等于 `Exclude<'name'|'age'|'gender', 'age'>`，返回的结果就是 `'name'|'gender`
- 然后遍历 `'name'|'gender'`，第一次循环 `P` 就是 `name`，返回 `T[P]` 就是 `User['name']`
- 第二次循环 `P` 就是 `gender`，返回 `T[P]` 就是 `User['gender']`，然后循环结束
- 结果就是 `{ name: string, gender: string }`

### 四、Pick

`Pick<T, K>` ：作用是取出 `T(对象类型)` 里边儿的 `K`，返回。

好像和 `Omit` 刚好相反，`Omit` 是不要 `K` ，`Pick` 是只要 `K`

传参方式和 `Omit` 是一样的，就不赘述了，用法示例：

```ts
type User = {
    name: string
    age: number
    gender: string
}
type T1 = Pick<User, 'name' | 'gender'>
// type T1 = { name: string, gender: string }
```

源码定义

```ts
type Pick<T, K extends keyof T> = { [P in K]: T[P]; }
```

- 可以看到等号左边做了泛型约束，限制了第二个参数 `K` 必须是第一个参数 `T` 里的属性。
- 如果第二个参数传入联合类型，会触发分发，以此来确保准确性，联合类型中的每一个单独类型都必须是第一个对象类型中的属性(不限制的话右边就要出错了)
- 参数都正确之后，等号右边的逻辑其实就是和 `Omit` 一模一样的了，直接遍历 `K`，取出返回就完事儿了

### 五、Record

`Record<K, T>`：作用是自定义一个对象。`K` 为对象的 `key` 或 `key` 的类型，`T` 为 `value` 或 `value` 的类型。

你有没有这样用过 ↓

```ts
const obj:any = {}
```

反正我有，其实用 `Record` 定义对象，在工作中还是很好用的，而且非常灵活，不同的对象定义上也会有一点区别，如下

**空对象**

```ts
// never，会限制为空对象
// any 指的是 string | number | symbol 这几个类型都行
type T1 = Record<any, never>
let obj1:T1 = {} 	// ok
// let obj1: T1 = {a:1} 这样不行，只能是空对象
```

**任意对象**

```ts
// 任意对象，unknown 或 {} 表示对象内容不限，空对象也行
type T1 = Record<any, unknown>
// 或
type T1 = Record<any, {}>
let obj2:T1 = {} 	// ok
let obj3:T1 = {a:1}  // ok
```

**自定义对象 key**

```ts
type keys = 'name' | 'age'
type T1 = Record<keys, string>
let obj1:T1 = {
    name: '沐华',
    age: '18'
    // age: 18  报错，第二个参数 string 表示 value 值都只能是 string 类型
}

// 如果需要 value 是任意类型，下面两个都行
type T2 = Record<keys, unknown>
type T3 = Record<keys, {}>
```

**自定义对象 value**

```ts
type keys = 'a' | 'b'
// type 或 interface 都一样
type values<T> = {
    name?: T,
    age?: T,
    gender?: string
}

// 自定义 value 类型
type T1 = Record<keys, values<number | string>>
let obj:T1 = {
    a: { name: '沐华' },
    b: { age: 18 }
}

// 固定 value 值
type T2 = Record<keys, 111>
let obj1:T2 = {
    a: 111,
    b: 111
}
```

源码定义

```ts
type Record<K extends any, T> = { [P in K]: T; }
```

左边限制了第一个参数 `K` 只能是 `string | number | symbol` 类型，可以是联合类型，因为右边遍历 `K` 了，然后遍历出来的每个属性的值，直接赋值为传入的第二个参数

### 六、Partial

`Partial<T>`：作用生成一个将 `T(对象类型)` 里所有属性都变成可选的之后的新类型

示例如下：

```ts
 type User = {
    name: string
    age: number
}
type T1 = Partial<User>
// 简单说 T1 和 T2 是一模一样的
type T2 = {
    name?: string
    age?: number
}
```

源码定义

```ts
type Partial<T> = { [P in keyof T]?: T[P]; }
```

这下看源码定义的是不是特别简单，就是循环传进来的对象类型，给每个属性加个 `?` 变成可选属生

### 七、Required

`Required<T>`：作用和 `Partial<T>` 刚好相反，`Partial` 是返回所有属性都是**非必填**的对象类型，而 `Required` 则是返回所有属性都是**必填项**的对象类型。参数 `T` 也是一个对象类型。

示例：

```ts
 type User = {
    name?: string
    age?: number
}
type T1 = Required<User>
// 简单说 T1 和 T2 是一模一样的
type T2 = {
    name: string
    age: number
}
```

源码定义

```ts
type Required<T> = { [P in keyof T]-?: T[P]; }
```

和 `Partial` 的源码定义相比基本一样的，只是这里多了个减号 `-`，没错，就是减去的意思，`-?` 就是去掉 `?`，然后就变成必填项了，这样解释是不是很好理解

### 八、Readonly

`Readonly<T>` ：作用是返回一个所有属性都是只读不可修改的对象类型，与 `Partial` 和 `Required` 是非常相似的。参数 `T` 也是一个对象类型。

示例：

```ts
 type User = {
    name: string
    age?: number
}
type T1 = Readonly<User>
// 简单说 T1 和 T2 是一模一样的
type T2 = {
    readonly name: string
    readonly age?: number
}

type Readonly<T> = { readonly [P in keyof T]: T[P]; }
```

### 九、NonNullable

`NonNullable<T>`：作用是去掉 `T` 中的 `null` 和 `undefined`。`T` 为字面量/具体类型的联合类型，如果是对象类型是没有效果的。如下

```ts
 type T1 = NonNullable<string | number | undefined>;
// type T1 = string | number

type T2 = NonNullable<string[] | null | undefined>;    
// type T2 = string[]

type T3 = {
    name: string
    age: undefined
}
type T4 = NonNullable<T3> // 对象是不行的
```

源码定义

```ts
 // 4.8版本之前的版本
type NonNullable<T> = T extends null | undefined ? never : T;
// 4.8
type NonNullable<T> = T & {}
```

`TS 4.8版本` 之前的就是用一个三元表达式来过滤 `null | undefined`。而在 `4.8` 版本直接就是 `T & {}`，这是什么原理呢？其实是因为这个版本对 `--strictNullChecks` 做了增加，这主要体现还是在联合类型和交叉类型上，为什么这么说？

在 `js` 中都知道万物皆对象，原型链的最终点的正常对象就是 `Object` 了(`null` 算不正常的)，数据类型都是在原型链中继承于 `Object` 派生出来的。

在 `ts` 中也一样，由于 `{}` 是一个空对象，所以除了 `null` 和 `undefined` 之外的基础类型都可以视作继承于 `{}` 派生出来的。或者说如果一个值不是 `null` 和 `undefined` 就等于 `这个值 & {}` 的结果，如下

```ts
 type T1 = 'a' & {};  // 'a'
type T2 = number & {};  // number
type T3 = object & {};  // object
type T4 = { a: string } & {};  // { a: string }
type T5 = null & {};  // never
type T6 = undefined & {};  // never
```

如果 `T & {}` 中的 `T` 不是 `null/undefined` 就可以认为它肯定符合 `{}` 类型，就可以把 `{}` 从交叉类型中去掉了，如果是，则会被判为 `never`，而 `never` 是会被忽略的(上面 `Exclude` 源码定义里有提到)，所以在结果里自然就排除掉了 `null` 和 `undefined`。

还有如果 `T & {}` 中的 `T` 是联合类型，是会触发分发的

### 十、Awaited

`Awaited<T>`：作用是获取 `async/await` 函数或 `promise` 的 `then()` 方法的返回值的类型。而且自带递归效果，如果是这样嵌套的异步方法，也能拿到最终的返回值类型

示例：

```ts
 // Promise
type T1 = Awaited<Promise<string>>;
// type T1 = string

// 嵌套 Promise，会递归
type T2 = Awaited<Promise<Promise<number>>>;
// type T2 = number

// 联合类型，会触发分发
type T3 = Awaited<boolean | Promise<number>>;
// type T3 = number | boolean
```

来看下源码定义，看下到底是怎么执行的，是怎么拿到结果的呢？

```ts
 // 源码定义
type Awaited<T> = T extends null | undefined
	? T
	: T extends object & { then(onfulfilled: infer F): any }
		? F extends (value: infer V, ...args: any) => any
			? Awaited<V>
			: never
		: T
```

泛型条件有点多，就换了下行，方便看

- 如果 `T` 是 `null` 或 `undefined` 就直接返回 `T`

- 如果 `T` 是对象类型，并且里面有 `then`方法，就用 `infer`  类型推断出 `then`

  方法的第一个参数`onfulfilled`的类型赋值给 `F`，`onfulfilled`其实就是我们熟悉的 `resolve`。所以这里可以看出或者准确的说，`Awaited` 拿的不是 `then()` 的返回值类型，而是`resolve()` 的返回值类型

  - 既然 `F`是回调函数 `resolve`，就推断出该函数第一个参数类型赋值给 `V`，`resolve`的参数自然就是返回值

  - 传入 `V` 递归调用

  - `F` 不是函数就返回 `never`

- 如果 `T` 不是对象类型 或者 是对象但没有 `then` 方法，返回 `T` ，就是最后一行的 `T`

### 十一、Parameters

`Parameters<T>`：作用是获取函数所有参数的类型集合，返回的是元组。`T` 自然就是函数了

使用示例：

```ts
 declare function f1(arg: { a: number; b: string }): void;

// 没有参数的函数
type T1 = Parameters<() => string>;
// type T1 = []

// 一个参数的函数
type T2 = Parameters<(s: string) => void>;
// type T2 = [s: string]

// 泛型参数的函数
type T3 = Parameters<<T>(arg: T) => T>;
// type T3 = [arg: unknown]

// typeof f1 结果为 (arg: { a: number; b: string }) => void
type T4 = Parameters<typeof f1>;
// type T4 = [arg: {
//     a: number;
//     b: string;
// }]

// any 和 never
type T5 = Parameters<any>;
// type T5 = unknown[]
type T6 = Parameters<never>;
// type T6 = never

// 下面这样传参是会报错的
type T7 = Parameters<string>;
type T8 = Parameters<Function>;
 // 源码定义
type Parameters<T extends (...args: any) => any> = T extends (...args: infer P) => any ? P : never
```

可以看到限制了函数类型，然后 `...args` 取参数和 `js` 中的用法是一样的，`infer` 表示待推断的类型变量，打断出 `...args` 取到的类型赋值给 `P`

### 十二、ReturnType

`ReturnType<T>`：作用是获取函数返回值的类型。`T` 为函数

示例：

```ts
 declare function f1(): { a: number; b: string };

type T1 = ReturnType<() => string>;
// type T1 = string

type T2 = ReturnType<(s: string) => void>;
// type T2 = void

type T3 = ReturnType<<T>() => T>;
// type T3 = unknown

type T4 = ReturnType<<T extends U, U extends number[]>() => T>;
// type T4 = number[]

type T5 = ReturnType<typeof f1>;
// type T5 = {
//     a: number;
//     b: string;
// }

// any 和 never
type T6 = ReturnType<any>;
// type T6 = any
type T7 = ReturnType<never>;
// type T7 = never

// 下面这样是不行的
type T8 = ReturnType<string>;
type T9 = ReturnType<Function>;
 // 源码定义
type ReturnType<T extends (...args: any) => any> = T extends (...args: any) => infer R ? R : any
```

可以看到源码定义上和 `Parameters` 是基本一样的，只是把类型推断的参数换成返回值了

### 十三、ConstructorParameters/InstanceType

我们知道 `Parameters` 和 `ReturnType` 这一对是获取普通/箭头函数的**参数类型集合**以及**返回值类型**的了，还有一对组合`ConstructorParameters` 和 `InstanceType` 是获取**构造函数**的参数类型集合以及**返回值类型**的。

### 十四、Uppercase/Lowercase

Uppercase的作用是转换全部字母大写，Lowercase的作用是转换全部字母小写。

```ts
 type T1 = Uppercase<"abcd">
// type T1 = "ABCD"

type T2 = Lowercase<"ABCD">
// type T2 = "abcd"
```

### 十五、Capitalize/Uncapitalize

Capitalize的作用是转换首字母大小写，Uncapitalize的作用是转换非首字母大小写。

```ts
 type T1 = Capitalize<"abcd efg">
// type T1 = "Abcd efg"

type T2 = Uncapitalize<"ABCD EFG">
// type T2 = "aBCD EFG"
```
