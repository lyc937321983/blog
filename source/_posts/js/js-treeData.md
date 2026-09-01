---
title: javascript tree结构数据
date: 2022-02-17 09:45:22
tags: tree
comment: 'valine'
categories: 
- js
---


# tree结构数据

# 1.扁平数据转tree数据

- 首先，随意书写一段有关系的数据：

```js
const data = [
  { name: "马云", key: 1 },
  { name: "前端技术专家", key: 3, parent: 2 },
  { name: "首席科学家", key: 2, parent: 1 },
  { name: "前端架构师", key: 4, parent: 3 },
  { name: "前端工程师", key: 5, parent: 4 },
  { name: "前端菜鸟", key: 6, parent: 5 },
  { name: "前端小白", key: 7, parent: 6 },
  { name: "马岳父", key: 8 },
  { name: "QQ", key: 9, parent: 8 },
  { name: "微信", key: 10, parent: 8 },
  { name: "王者荣耀", key: 11, parent: 8 },
  { name: "绝地求生", key: 12, parent: 8 },
  { name: "QQ会员", key: 13, parent: 9 },
  { name: "QQ空间", key: 14, parent: 9 },
  { name: "QQ钱包", key: 15, parent: 9 },
  { name: "沙漠地图", key: 16, parent: 12 },
  { name: "公众号", key: 17, parent: 10 },
  { name: "群聊", key: 18, parent: 10 },
  { name: "小程序", key: 19, parent: 10 },
  { name: "花木兰", key: 20, parent: 11 },
  { name: "芈月", key: 21, parent: 11 },
  { name: "马可波罗", key: 22, parent: 11 },
];
```

- 如何得到这样的层次结构数据？
- 我们一个一个来看；数据中的key代表当前数据的唯一标识，而parent则代表其父级。我们要做的就是：遍历data数组，根据parent和key的对应关系，生成tree数据，这里我们用children来装子数据。

```js
//封装getTree方法，将数组转化为tree.

import { cloneDeep } from "lodash";//lodash库里引入cloneDeep深克隆方法

const getTree = (data) => {

  const temp = cloneDeep(data);//深克隆一份外来数据data，以防下面的处理修改data本身
  const parents = temp.filter((item) => !item.parent); //过滤出最高父集
  const children = temp.filter((item) => item.parent);//过滤出孩子节点
  
  //遍历孩子节点，根据孩子的parent从temp里面寻找对应的node节点，将孩子添加在node的children属性之中。
  children.map((item) => {
    const node = temp.find((el) => el.key === item.parent);
    node && (node.children ? node.children.push(item) : node.children = [item]);
  });
  return parents;//返回拼装好的数据。
};
```

- 封装好geTree方法，调用：

```js
const tree=getTree(data);
console.log(tree);
```

- 数组转tree的内容到这里就结束了。

# 2.tree数据转扁平数据

- 数组转tree会了，那接下来就是tree转数组了。
- 先随意定义一段数据：

```js
const treeData = [
  {
    title: "太极",
    key: 1,
    children: [
      {
        title: "两仪",
        key: 2,
        parent: 1,
        children: [
          {
            title: "老阳",
            key: 3,
            parent: 2,
            children: [{ title: "乾卦", key: 7, parent: 3 }],
          },
          {
            title: "少阳",
            key: 4,
            parent: 2,
            children: [
              { title: "巽卦", key: 9, parent: 4 },
              { title: "兑卦", key: 10, parent: 4 },
              { title: "离卦", key: 11, parent: 4 },
            ],
          },
          {
            title: "老阴",
            key: 5,
            parent: 2,
            children: [{ title: "坤卦", key: 8, parent: 5 }],
          },
          {
            title: "少阴",
            key: 6,
            parent: 2,
            children: [
              { title: "艮卦", key: 12, parent: 6 },
              { title: "坎卦", key: 13, parent: 6 },
              { title: "震卦", key: 14, parent: 6 },
            ],
          },
        ],
      },
    ],
  },
];
```

- 子曰：“加我数年，五十以学易，可以无大过矣。”可见孔子对易经的推崇。以上数据是易经中太极到八卦演化过程，甚为奇妙，可以说宇宙万象皆在这演化规律之中：太极->两仪->四象->八卦。我们要做的就是将这一段tree形数据转化为扁平结构。话不多说，直接上才艺。

```js
const getFlatData=(data)=>{
    const flatData=[];
    data.map(item=>{
      //解构当前item,去掉item中的children，将纯净的node（不含children的单条数据）添加到flatData中。如果children存在就递归执行getFlatData方法，直至添加了所有。
        const {children,...node}=item
        flatData.push(node);
        children&&flagData.push(...getFlatData(children));
    })
    return flatData;
};
```

- 方法写好了，执行代码：

```js
  console.log(getFlatData(treeData));
```

- tree数据转扁平结构到这就结束了。