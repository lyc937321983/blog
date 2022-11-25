---
title: Vue3+TS项目开发使用笔记
date: 2022-01-07 16:00:22
tags: Vue3
comment: 'valine'
categories: 
- vue

---



# Vue3-TS项目开发使用笔记

## 概述

Vue3出来已经有一段时间了，在团队中，也进行了大量的业务实践，也有了一些自己的思考。

总的来说，Vue3无论是在底层的原理上，还是在业务的实际开发中，都有了长足的进步。

使用 proxy 代替之前的 Object.defineProperty 的API，性能更加优异，也解决了之前vue在处理对象、数组上的缺陷；在diff算法上，使用了静态标记的方式，大大提升了Vue的执行效率。

在使用的层面，我们从options Api，变成了composition Api，慢慢的在实际的业务中，我们抛弃了原本的data、methods、computed那种隔离式的写法。compositon Api，它更加聚焦，它讲究的是相关业务的聚合性。同时，在composition Api中，为了防止过于重的业务逻辑，它提供了一种关注点分离的方式，大大的提升了我们代码的可读性。

完全良好的支持了TypeScript，类型校验也成为了以后Vue3进行大型项目开发的质量保障，同时这也是面向了趋势 -- 前端的未来就是TypeScript！

## 1、compositon Api

compositon Api的本质，体现在代码里面，也就是一个setup函数，在这个setup函数中，返回的数据，会用到该组件的模板中。return的这个对象，一定程度上，代表了之前vue2中的data属性。

```js
import { defineComponent, ref } from 'vue';
export default defineComponent({
    name: 'Gift',
    setup() {
        const counter = ref(0);
        return {
            counter
        }
    }
})
复制代码
```

这时候，对于大多数初学者来说，可能存在的疑惑就是，那么我能不能定义options Api的写法，比如data、computed、watch、methods等等。

这里我需要明确的是，Vue3是完全兼容Vue2的这种options Api的写法，但是从理念上来说，更加推荐setup的方式，来写我们的组件。原因如下：Vue3的存在，本身是为了解决Vue2的问题的，Vue2的问题就是在于，聚合性不足，会导致代码越来越臃肿！setup的方式，能够让data、方法逻辑、依赖关系等聚合在一块，更方便维护。

也就是说，以后我们尽量不要写单独的data、computed、watch、methods等等，不是Vue3不支持，而是和Vue3的理念违背。

components属性，也就是一个组件的子组件，这个配置在Vue2和3的差异不大，Vue2怎么用，Vue3依然那么用。

### 1、ref 和 reactive的区别？

在功能方面，ref 和 reactive，都是可以实现响应式数据！

在语法层面，两个有差异。ref定义的响应式数据需要用[data].value的方式进行更改数据；reactive定义的数据需要[data].[prpoerty]的方式更改数据。

```js
const actTitle: Ref<string> = ref('活动名称');

const actData = reactive({
    list: [],
    total: 0,
    curentPage: 1,
    pageSize: 10
});

actTitle.value = '活动名称2';

actData.total = 100;
复制代码
```

但是在应用的层面，还是有差异的，通常来说：单个的普通类型的数据，我们使用ref来定义响应式。表单场景中，描述一个表单的key:value这种对象的场景，使用reactive；在一些场景下，某一个模块的一组数据，通常也使用reactive的方式，定义数据。

那么，对象是不是非要使用reactive来定义呢？其实不是的，都可以，根据自己的业务场景，具体问题具体分析！ref他强调的是一个数据的value的更改，reactive强调的是定义的对象的某一个属性的更改。

### 2、周期函数

周期函数，在Vue3中，是被单独使用的，使用方式如下：

```js
import { defineComponent, ref, onMounted } from 'vue';
export default defineComponent({
    name: 'Gift',
    setup() {
        const counter = ref(0);
        onMounted(() => {
            // 处理业务，一般进行数据请求
        })
        return {
            counter
        }
    }
})

复制代码
```

### 3、store使用

在Vue2中，其实可以直接通过this.$store进行获取，但是在Vue3中，其实没有this这个概念，使用方式如下：

```js
import { useStore } from "vuex";
import { defineComponent, ref, computed } from 'vue';
export default defineComponent({
    name: 'Gift',
    setup() {
        const counter = ref(0);
        const store = useStore();
        const storeData = computed(() => store); // 配合computed，获取store的值。
        return {
            counter,
            storeData
        }
    }
})
复制代码
```

### 4、router的使用

在Vue2中，是通过this.$router的方式，进行路由的函数式编程，但是Vue3中，是这么使用的：

```js
import { useStore } from "vuex";
import { useRouter } from "vue-router";
import { defineComponent, ref, computed } from 'vue';
export default defineComponent({
    name: 'Gift',
    setup() {
        const counter = ref(0);
        const router = useRouter();
        const onClick = () => {
            router.push({ name: "AddGift" });
        }
        return {
            counter,
            onClick
        }
    }
})
复制代码
```

## 2、关注点分离

关注点分离，应该分两层意思：第一层意思就是，Vue3的setup，本身就把相关的数据，处理逻辑放到一起，这就是一种关注点的聚合，更方便我们看业务代码。

第二层意思，就是当setup变的更大的时候，我们可以在setup内部，提取相关的一块业务，做到第二层的关注点分离。

```js
import { useStore } from "vuex";
import { useRouter } from "vue-router";
import { defineComponent, ref, computed } from 'vue';
import useMerchantList from './merchant.js';
export default defineComponent({
    name: 'Gift',
    setup() {
        const counter = ref(0);
        const router = useRouter();
        const onClick = () => {
            router.push({ name: "AddGift" });
        }
        // 在该示例中，我们把获取商家列表的相关业务分离出去。也就是下面的merchant.ts
        const {merchantList} = useMerchantList();
        return {
            counter,
            onClick,
            merchantList
        }
    }
})
复制代码
```

merchant.ts

```ts
import { getMerchantlist } from "@/api/rights/gift";
import { ref, onMounted } from "vue";

export default function useMerchantList(): Record<string, any> {
  const merchantList = ref([]);
  const fetchMerchantList = async () => {
    let res = await getMerchantlist({});
    merchantList.value = res?.data?.child;
  };

  onMounted(fetchMerchantList);

  return {
    merchantList
  };
}
复制代码
```

## 3、TypeScript支持

这一部分内容，准确的来说，是TS的内容，不过它与Vue3项目开发，息息相关，所以真的想用Vue3，我们还是得了解TS的使用。

不过这一部分，我不会介绍TS的基础语法，主要是在业务场景中，如何组织TS。

使用TS进行业务开发，一个核心的思维是，先关注数据结构，再根据数据结构进行页面开发。以前的前端开发模式是，先写页面，后关注数据。

比如要写一个礼品列表的页面，我们可能要定义这么一些interface。总而言之，我们需要关注的是：页面数据的interface、接口返回的数据类型、接口的入参类型等等。

```ts
// 礼品创建、编辑、列表中的每一项，都会是这个数据类型。
interface IGiftItem {
  id: string | number;
  name: string;
  desc: string;
  [key: string]: any;
}

// 全局相应的类型定义
// 而且一般来说，我们不确认，接口返回的类型到底是什么（可能是null、可能是对象、也可能是数组），所以使用范型来定义interface
interface IRes<T> {
    code: number;
    msg: string;
    data: T
}
// 接口返回数据类型定义

interface IGiftInfo {
    list: Array<IGiftItem>;
    pageNum: number;
    pageSize: number;
    total: number;
}
复制代码
```

在一个常见的接口请求中，我们一般使用TS这么定义一个数据请求，数据请求的req类型，数据请求的res类型。

```ts
export const getGiftlist = (
  params: Record<string, any>
): Promise<IRes<IGiftInfo>> => {
  return Http.get("/apis/gift/list", params);
};
```

