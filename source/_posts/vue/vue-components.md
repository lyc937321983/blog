---
title: vue2 组件通信方式
date: 2023-12-13 11:25:22
tags: 组件传值
comment: 'valine'
categories: 
- vue
---
# vue2 组件通信方式

## 1.父子组件传值

 父组件通过 props 属性向子组件传递数据，子组件通过 $emit 方法向父组件传递事件。
 vue组件通信方式：
-   props
-   emit
-   vuex
 下面是一个简单的例子：

```html
<!-- 父组件 -->
<template>
  <div>
    <child-component :message="message" @send="handleSend"></child-component>
  </div>
</template>
<script>
import ChildComponent from './ChildComponent.vue';
export default {
  components: {
    ChildComponent
  },
  data() {
    return {
      message: ''
    };
  },
  methods: {
    handleSend(message) {
      console.log(message);
    }
  }
};
</script>
<!-- 子组件 -->
<template>
  <div>
    <input type="text" v-model="message">
    <button @click="handleSend">发送</button>
  </div>
</template>

<script>
export default {
  props: {
    message: String
  },
  data() {
    return {
      value: ''
    };
  },
  methods: {
    handleSend() {
      this.$emit('send', this.message);
    }
  }
};
</script>
```
在上面的代码中，父组件通过 props 属性向子组件传递了一个名为 message 的数据，子组件通过 $emit 方法向父组件传递了一个名为 send 的事件，并将 message 数据作为参数传递给父组件。父组件通过 @send 监听子组件的 send 事件，并在 handleSend 方法中获取 message 数据。

## 1.兄弟组件传值
在 Vue.js 中。兄弟组件可以通过父组件的 props 属性来传递数据，通过 $emit 方法来触发事件。下面是一个简单的例子：

```html
<!-- 父组件 -->
<template>
  <div>
    <child-component-1 :message="message" @send="handleSend"></child-component-1>
    <child-component-2 :message="message"></child-component-2>
  </div>
</template>

<script>
import ChildComponent1 from './ChildComponent1.vue';
import ChildComponent2 from './ChildComponent2.vue';

export default {
  components: {
    ChildComponent1,
    ChildComponent2
  },
  data() {
    return {
      message: ''
    };
  },
  methods: {
    handleSend(message) {
      this.message = message;
    }
  }
};
</script>

<!-- 子组件1 -->
<template>
  <div>
    <input type="text" v-model="message">
    <button @click="handleSend">发送</button>
  </div>
</template>

<script>
export default {
  props: {
    message: String
  },
  data() {
    return {
      value: ''
    };
  },
  methods: {
    handleSend() {
      this.$emit('send', this.message);
    }
  }
};
</script>

<!-- 子组件2 -->
<template>
  <div>
    <p>{{ message }}</p>
  </div>
</template>

<script>
export default {
  props: {
    message: String
  }
};
</script>
```
在上面的代码中，父组件包含了两个子组件：ChildComponent1 和 ChildComponent2。ChildComponent1 通过 props 属性向父组件传递了一个名为 message 的数据，通过 $emit 方法向父组件传递了一个名为 send 的事件，并将 message 数据作为参数传递给父组件。父组件接收到子组件1的 send 事件后，将 message 数据保存在自己的 data 中，并通过 props 属性将 message 数据传递给 ChildComponent2。

## 3.跨级组件传值
 跨级组件可以通过父组件的 props 属性来传递数据，通过 $emit 方法来触发事件。下面是一个简单的例子：

```html
<!-- 父组件 -->
<template>
  <div>
    <child-component-1 :message="message" @send="handleSend"></child-component-1>
    <child-component-3 :message="message"></child-component-3>
  </div>
</template>

<script>
import ChildComponent1 from './ChildComponent1.vue';
import ChildComponent3 from './ChildComponent3.vue';

export default {
  components: {
    ChildComponent1,
    ChildComponent3
  },
  data() {
    return {
      message: ''
    };
  },
  methods: {
    handleSend(message) {
      this.message = message;
    }
  }
};
</script>

<!-- 子组件1 -->
<template>
  <div>
    <input type="text" v-model="message">
    <button @click="handleSend">发送</button>
  </div>
</template>

<script>
export default {
  props: {
    message: String
  },
  data() {
    return {
      value: ''
    };
  },
  methods: {
    handleSend() {
      this.$emit('send', this.message);
    }
  }
};
</script>

<!-- 子组件3 -->
<template>
  <div>
    <p>{{ message }}</p>
  </div>
</template>

<script>
export default {
  props: {
    message: String
  }
};
</script>
```
在上面的代码中，父组件包含了两个子组件：ChildComponent1 和 ChildComponent3。ChildComponent1 通过 props 属性向父组件传递了一个名为 message 的数据，通过 $emit 方法向父组件传递了一个名为 send 的事件，并将 message 数据作为参数传递给父组件。父组件接收到子组件1的 send 事件后，将 message 数据保存在自己的 data 中，并通过 props 属性将 message 数据传递给 ChildComponent3。

## 4.Vuex 状态管理
在 Vue.js 中，组件传值的另一种方式是使用 Vuex 状态管理。Vuex 是一种状态管理模式，用于管理应用程序中的共享状态。Vuex 中的状态可以被任何组件访问和修改，因此可以用来实现组件之间的通信。下面是一个简单的例子：
```html
<!-- 父组件 -->
<template>
  <div>
    <child-component-1></child-component-1>
    <child-component-3></child-component-3>
  </div>
</template>

<script>
import ChildComponent1 from './ChildComponent1.vue';
import ChildComponent3 from './ChildComponent3.vue';
import store from './store';

export default {
  components: {
    ChildComponent1,
    ChildComponent3
  },
  store
};
</script>

<!-- 子组件1 -->
<template>
  <div>
    <input type="text" v-model="message">
    <button @click="handleSend">发送</button>
  </div>
</template>

<script>
import { mapActions } from 'vuex';

export default {
  data() {
    return {
      message: ''
    };
  },
  methods: {
    ...mapActions(['sendMessage']),
    handleSend() {
      this.sendMessage(this.message);
    }
  }
};
</script>

<!-- 子组件3 -->
<template>
  <div>
    <p>{{ message }}</p>
  </div>
</template>

<script>
import { mapState } from 'vuex';
export default {
  computed: {
    ...mapState(['message'])
  }
};
</script>
```
在上面的代码中，父组件通过引入 store 对象来使用 Vuex 状态管理。子组件1通过 mapActions 方法将 sendMessage 方法映射到组件中，并在 handleSend 方法中调用 sendMessage 方法来发送消息。sendMessage 方法将消息保存在 Vuex 的 state 中。子组件3通过 mapState 方法将 message 属性映射到组件中，并在模板中使用 message 属性来显示消息。

## 5.总结
本文详细介绍了 Vue.js 中的组件传值机制，包括父子组件传值、兄弟组件传值、跨级组件传值和使用 Vuex 状态管理等多种方式。在实际开发中，我们可以根据具体的场景和需求来选择合适的方式来实现组件之间的通信。同时，我们也需要注意传递数据的类型和格式，以保证数据的正确性和可靠性。