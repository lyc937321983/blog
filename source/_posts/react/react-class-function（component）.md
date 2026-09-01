---
title: React函数组件和类组件
date: 2022-06-01 14:28:22
tags: 函数组件和类组件
comment: 'valine'
categories: 
- react

---

# React-函数组件和类组件

### 类组件

**何谓类组件（Class Component)**

所谓类组件，就是基于 ES6 Class 这种写法，通过继承 React.Component 得来的 React 组件。以下是一个典型的类组件：

```js
class DemoClass extends React.Component {
  // 初始化类组件的 state
  state = {
    text: ""
  };

  componentDidMount() {
    // 省略业务逻辑
  }

  changeText = (newText) => {
    // 更新 state
    this.setState({
      text: newText
    });
  };

  // 编写生命周期方法 render
  render() {
    return (
      <div>
        <p>{this.state.text}</p>
        <button onClick={()=>this.changeText("newText")}>点我修改</button>
      </div>
    );
  }
}
```

### 特点

- 为了避免代码冗余，提高代码利用率，组件可以重复调用
- 组件的属性props是只读的，调用者可以传递参数到props对象中定义属性，调用者可以直接将属性作为组件内的属性或方法直接调用。往往是组件调用方调用组件时指定props定义属性，往往定值后就不改边了，注意组件调用方可赋值被调用方。
- 通过props的方式进行父子组件交互,通过传递一个新的props属性值使得子组件重新render，从而达到父子组件通讯。
- {...this.props}可以传递属性集合，...为属性扩展符
- 组件必须返回了一个 React 元素
- 组件中state为私有属性，是可变的，一般在construct()中定义，使用方法：不要直接修改 state(状态)
- 修改子组件还有一种方式，通过 ref属性，表示为对组件真正实例引用，其实就是ReactDOM.render()返回的组件实例

### 函数组件

**何谓函数组件/无状态组件（Function Component/Stateless Component）**

函数组件顾名思义，就是以函数的形态存在的 React 组件。早期并没有 React-Hooks 的加持，函数组件内部无法定义和维护 state，因此它还有一个别名叫“无状态组件”。以下是一个典型的函数组件：

```js
function DemoFunction(props) {
  const { text } = props
  return (
    <div className="demoFunction">
      <p>{`function 组件所接收到的来自外界的文本内容是：[${text}]`}</p>
    </div>
  );
}
```

### 特点

- 只负责接收 props，渲染 DOM
- 没有 state
- 返回了一个 React 元素
- 不能访问生命周期方法
- 不需要声明类：可以避免 extends 或 constructor 之类的代码，语法上更加简洁。
- 不会被实例化：因此不能直接传 ref（可以使用 React.forwardRef 包装后再传 ref）。
- 不需要显示声明 this 关键字：在 ES6 的类声明中往往需要将函数的 this 关键字绑定到当前作用域，而因为函数式声明的特性，我们不需要再强制绑定。
- 更好的性能表现：因为函数式组件中并不需要进行生命周期的管理与状态管理，因此React并不需要进行某些特定的检查或者内存分配，从而保证了更好地性能表现。

### 共同点

- 接收值：都一样接收了一个只读的 props
- 返回值：都是返回了一个 React 元素

### 不同点

#### 设计思想层面

> - 类组件的根基是 OOP（面向对象编程），所以它有继承、有属性、有内部状态的管理。
> - 函数组件的根基是 FP (函数式编程)。它属于“结构化编程”的一种，与数学函数思想类似。也就是假定输入与输出存在某种特定的映射关系，那么输入一定的情况下，输出必然是确定的。

#### 渲染差异

**函数组件会捕获render内部的状态**

> 具体文章参见：React 作者 Dan的文章[How Are Function Components Different from Classes](https://link.juejin.cn/?target=https%3A%2F%2Foverreacted.io%2Fhow-are-function-components-different-from-classes%2F)

#### 能力

- 类组件通过生命周期包装业务逻辑
- 函数组件可以通过**React Hooks** 钩子函数来模拟类组件中的生命周期（useState ，useEffect，useContext，useCallback 等）

> Hooks 的本质：一套能够使函数组件更强大、更灵活的“钩子”

### 使用场景

- 在不使用 Recompose 或者 Hooks 的情况下，如果需要使用生命周期，那么就用类组件，限定的场景是非常固定的；
- 但在 recompose 或 Hooks 的加持下，这样的边界就模糊化了，类组件与函数组件的能力边界是完全相同的，都可以使用类似生命周期等能力。

### 设计模式

在设计模式上，因为类本身的原因，**类组件**是可以实现继承的，而函数组件缺少继承的能力。

当然在 React 中也是不推荐继承已有的组件的，因为继承的灵活性更差，细节屏蔽过多，所以有这样一个铁律，**组合优于继承**。

### 发展趋势

**函数组件更加契合 React 框架的设计理念**

React 组件本身的定位就是函数，一个吃进数据、吐出 UI 的函数。作为开发者，我们编写的是声明式的代码，而 React 框架的主要工作，就是及时地把声明式的代码转换为命令式的 DOM 操作，把数据层面的描述映射到用户可见的 UI 变化中去。这就意味着从原则上来讲，React 的数据应该总是紧紧地和渲染绑定在一起的，而类组件做不到这一点。

由于 React Hooks 的推出，函数组件成了社区未来主推的方案。

React 团队从 Facebook 的实际业务出发，通过探索时间切片与并发模式，以及考虑性能的进一步优化与组件间更合理的代码拆分结构后，认为类组件的模式并不能很好地适应未来的趋势。 他们给出了3 个原因：

> - this 的模糊性；
> - 业务逻辑散落在生命周期中；
> - React 的组件代码缺乏标准的拆分方式。

而使用 Hooks 的函数组件可以提供比原先更细粒度的逻辑组织与复用，且能更好地适用于时间切片与并发模式。