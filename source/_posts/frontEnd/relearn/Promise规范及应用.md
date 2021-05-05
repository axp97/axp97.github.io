---
title: Promise规范及应用
date: 2021-04-04 15:15:00
author: 安小盼
categories: 前端
tags:
  - Javascript
  - 前端进阶
---

## 目标

**为什么学**

* Promise 重要性：async/await 语法的基础，JavaScript 中处理异步的标准形式。
* 深入理解 Promises 运行机制


**能学到什么**

* 熟悉 Promise A+ 规范
* 实现一个 Promise
* 简介 Generator 和 Async

## 知识要点

### PromiseA+规范

#### 1. 了解术语

* `promise` 是一个包含 then 方法的对象或函数，该方法符合规范指定的行为。
* `thenable` 是一个包含 then 方法和对象或者函数。
* `value` 就是任意合法 JS 值。
* `exception` 就是 throw 语句抛出的值。
* `reason` 是一个指示 promise 为什么被 rejected 的值。

#### 2. Promise 状态

* **pending**
初始状态，可改变
在 resolve 和 reject 前都处于这个状态

    ```
    --> resolve(value) --> fulfilled
    --> reject(reason) --> rejected
    ```

* **fulfilled**
一次性特征：最终态，不可变
必须拥有一个 value 值

* **rejected**
最终态，不可变
必须拥有一个 reason

#### 3. Then 方法

promise 必须有 then 方法，接受 `onFulfilled` 和 `onRejected` 参数。

##### 3.1. 参数规范

* 函数：必须最多执行一次
* 非函数：忽略，返回默认值/函数

##### 3.2. 参数特性

微任务阶段执行，执行时机：

* promise  --> fulfilled/rejected 时，调用 onFulFilled(value) / onRejected(reson)
* 在 promise 变成 fulfilled/rejected 之前，不应该调用 onFulfilled/onRejected
* 只能调用一次
    
#### 4. then 方法可以调用多次

执行时机：

promise --> fulfilled / rejected 后，按照注册的顺序执行

#### 5. 返回值：promise

若返回非函数：

promise2 以 promise1 的 value/reson 触发 fulfilled

返回函数：

onFulfilled / onRejected 执行，过程中抛出e，promise2 需要 reject

结果为 x，调用 `resolvePromise`

#### 6. resolvePromise(promise2, x, resolve, reject)

##### 6.1 参数

* promise2：当前第一个 promise 的返回
* x：onFulfilled/onRejected 执行结果

##### 6.2 规范

**1. 避免死循环**

```javascript 
if (promise2 === x)｛
    reject typeError
｝
```

**2. type x**

* promise，取决于 x 的状态
pending：promise 必须在 pending 状态，直到 x 的状态变更
fulfilled：value -> fulfilled
rejected：reason -> rejected


* Object || Function
`const then = x.then`

如果报错，reject reson

* 非函数：resolve(x)
* 函数：then.call(x, resolvePromiseFn, rejectPromiseFn)

## 补充知识点

### 迭代器 Iterator

ES6 引入的一种新的遍历机制，也是一种特殊对象，它具有一些专门为迭代过程设计的专有接口。

可调用 next() 方法，返回一个当前结果对象。

**next()方法：**

value：当前属性的值
done：用于判断是否遍历结束，当没有更多可返回的数据时，返回true

### 生成器 Generator

生成器是一种返回迭代器的函数，通过function关键字后的星号(*)来表示，函数中会用到新的关键字yield。星号可以紧挨着function关键字，也可以在中间添加一个空格.


### Async 和 Await

实际：Promise 语法糖，同步的形式写

![xmind总结](/static/xmind/frontEnd/relearn/promise.png)

