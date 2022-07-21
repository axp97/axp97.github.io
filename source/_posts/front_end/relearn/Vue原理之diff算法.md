---
title: Vue原理之diff算法
date: 2021-05-30 10:30:00
author: 安小盼
categories: 前端
tags:
    - Vue
    - diff
    - 前端进阶
---

## 目标

**为什么学**

-   高级前端工具，大多离不开算法

**能学到什么**

-   深入理解 diff 算法

## 知识要点

### 一、 前置知识

#### 1. diff 算法简介

* 上古时期：O(m^3n^3)
* 2011 年：O(n^3)，n为节点总数
* React 提出近代同层比较算法：O(n)

**举个栗子理解下时间复杂度，遍历一个长度为 n 的列表：**

* 最好时间复杂度：1（第一个就找到）
* 最坏时间复杂度：n（最后一个元素找到）
* 平均时间复杂度：总操作数 / 总情况数 = 1 + 2 + 3 ... + n / (n + 1(not found)) = n
* 均摊时间复杂度：最坏时间复杂度均摊 = n / n = 1

#### 2. 为什么需要 diff

* 本质上为了性能
* 数据与视图隔离解耦：f(state) -> View

#### 3. Why O(n^3)

传统算法是跨层级比较，即计算机中跨层级比较两棵树。

* 树的最短编辑距离算法复杂度是 O(n^2)
  * 经典解决方法：Levenshtein（莱文斯坦）算法，复杂度o(n^2)
* diff 再做一次 patch，找到差异后还要计算最小转换方式，再遍历一次
* 累计 O(n^3)

#### 4. Why O(n)

React/Vue 的 diff 复杂度其实不是严格意义上的 O(n)。
复杂度其实是 O(nm)，只是有一些技巧可以优化成 O(n)。

```js
 /*
 *   a       a
 *  | |     | |
 *  c b     c d
 * 只做同层比较：
 * [a, a] 相同，不处理
 * [c, c] 相同，不处理
 * [b, d] 不相同，替换
 */
const arr = [a, b, c]
const newArr = [b, d, e, f]
/* 比较过程
 * [a, b]
 * [b, d]
 * [c, e]
 * [null, f]
 */
for (let i = 0, len = oldNodes.length; i < len; i++) {
  if (oldNodes[i].type !== newNodes[i].type) {
    replace()
  }
  // 如果没有这一层，假设 type 全不相同，那么就是 O(n)，最坏复杂度 O(nm)
  else if (oldNodes[i].children && oldNodes[i].children.length) { 
  }
}
```

当然，这只是 React 的实现，肯定还是有更优秀的 diff 算法的，比如 inferno 的，能到 O(mlogn)，vue3 也是借鉴了这一算法

#### 4. How O(n)

React 是怎么设计将复杂度砍下来呢？

其实就是在算法复杂度、虚拟 DOM 渲染机制、性能中找了一个平衡，react 采用了启发式的算法，做了如下最优假设：

 * 如果节点类型相同，那么以该节点为根节点的 tree 结构，大概率是相同的，所以如果类型不同，可以直接「删除」原节点，「插入」新节点
 * 跨层级移动子 tree 结构的情况比较少见，或者可以培养用户使用习惯来规避这种情况，遇到这种情况同样是采用先「删除」再「插入」的方式，这样就避免了跨层级移动
 * 同一层级的子元素，可以通过 key 来缓存实例，然后根据算法采取「插入」「删除」「移动」的操作，尽量复用，减少性能开销
 * 完全相同的节点，其虚拟 DOM 也是完全一致的

#### 5. 聊一聊 key

首先，为啥要有 key，这种官方文档就有说明：https://cn.vuejs.org/v2/api/#key

* `key` 的特殊 attribute 主要用在 Vue 的虚拟 DOM 算法，在新旧 nodes 对比时辨识 VNodes
* 如果不使用 key，Vue 会使用一种最大限度减少动态元素并且尽可能的尝试就地修改/复用相同类型元素的算法。而使用 key 时，它会基于 key 的变化重新排列元素顺序，并且会移除 key 不存在的元素。

**举个栗子：**

假设原来有 [1, 2, 3] 三个子节点渲染了，假设我们这么操作了一波，将顺序打乱变成 [3, 1, 2]，并且删除了最后一个，变成 [3, 1]。

最优的 diff 思路应该是复用 3, 1 组件，移动一下位置，去掉 2 组件，这样整体是开销最小的，如果有 key 的话，这波操作水到渠成，如果没有 key 的话，那么就要多一些操作了:

* 判断哪些可以复用，有 key 只需要从映射中康康 3, 1在不在，没有 key 的话，可能就执行替换了，肯定比「复用」「移动」开销大了
* 删除了哪一个？新增了哪一个？有 key 的话是不是很好判断嘛，之前的映射没有的 key，比如变成 [3, 1, 4] 那这个 4 很容易判断出应该是新建的，删除也同理
* 但是没有 key 的话就麻烦一些了

**key 的操作误区**

* 使用随机数，会导致每次 key 都不一样，那复用毛线，都是新建了
* 使用数组下标，`splice` 操作中 Vue 行为不正常，React 性能不正常

### 二、 虚拟 DOM

#### 1. 什么是虚拟 DOM

虚拟 DOM 为嵌套结构的对象树，与 DOM 结构类似。一般记录它的节点类型、属性，还有子节点，结构如下：

```js
{
  type: 'div',
  props: {
    children: [

    ]
  },
  el: xxxx
}
```

#### 2. 怎么创建虚拟 DOM

定义一个方法（h、createElement）创建一个虚拟节点, 返回一个对象：

```js
function h(type, props) { return { type, props } }
```

#### 3. 如何使用虚拟 DOM

编译通过一些工具转成函数调用嵌套的方式，最终生成对象 tree。例如：JSX 可用 Babel 转一下 createElement 形式：

```js
JSX:
<div>
  <ul className='padding-20'>
    <li key='li-01'>this is li 01</li>
  </ul>
</div>

// 经过一些工具转一下：
createElement('div', {
  children: [
    createElement('ul', { className: 'padding-20' },
       createElement('li', { key: 'li-01'}, 'this is li 01'))
  ]
})
```

#### 4. 虚拟 DOM 的渲染(mount/render)

通过原生 DOM 的方法 createElement、insert、insertbefore 等 DOM 方法进行操作：

```js
f(vode) {
  document.createElement();
  ....

  parent.insert()
  . insertBefore
}

export const render = (vnode, parent) => {  }

<div id='app'></div>
```

#### 4. diff 相关了(patch)

改了数据后的 tree 与原 tree 做对比，找到最小差异部分 DOM 并更新：

```js
f(oldVnodeTree, newVnodeTree, parent) -> 调度? -> view
```

### 三、Vue3 diff 核心思想

1. 定义前后两个指针，先裁剪，缩小计算空间
2. 双指针找出中间差量部分，进行增加/删除
3. 移动部分：将最大长度不需要动的部分找出来，把只需要动的部分 insert 下
    * 利用最长上升子序列算法，选一个长度最长的依次上升的，保持不动，那他移动的步数一定是最少的


> 参考资料：
> 
> * [浏览器工作原理揭秘](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/)
> * [像素的一生](https://www.bilibili.com/video/av35265997/)
> * [Levenshtein](https://en.wikipedia.org/wiki/Levenshtein_distance)
> * [inferno](https://github.com/infernojs/inferno)
> * [启发式算法](https://www.zhihu.com/topic/19864220/hot)
> * [最长上升子序列算法](https://en.wikipedia.org/wiki/Longest_increasing_subsequence)

![xmind总结](/static/xmind/front_end/relearn/vue-diff.png)
