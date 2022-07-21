---
title: React详细上手指北
date: 2022-03-20 18:30:00
author: 安小盼
categories: 前端
tags:
    - React
---

## 目标

-   [x] `React` 基础
-   [x] `JSX` ✨
-   [x] 独立上手开发 `React` 项目！

## 知识要点

可以看每个小结的总结。

### 一、 React 基础

#### 1. JSX

##### 1.1. 定义

1. what:

-   官网定义：一个 JavaScript 的语法扩展；一个表达式；
-   ⾮常类似 DOM 的 xml 结构的语法，区别与在 jsx 中使用 `{}` 来使用 js 表达式

2. why:

-   官网：关注点分离，将 JSX 和 UI 放一起在视觉上有辅助作用

3. eg:

```js
<div className="foo">{something ? "something is
true" : "something is false"}</div>
```

##### 1.2. 合法的 jsx 元素

可用 `React.isValidElement` 判断

-   普通的 DOM 标签，如 div/p/span 等等
-   声明的 react 组件，例如通过 class 或函数创建的 jsx
    组件
-   null（最终会渲染⼀个空元素）
-   字符串（最终会渲染⼀个 text 节点）
-   对于数字类型，最终就会渲染出来，所以有的时候通过布尔表达式判断的时候就会有问题

##### 1.3. 总结

-   jsx 是⼀种语法糖，我们需要将他们编译为 `React.createElement` 的形式 ✨
-   写 jsx 需要注意类型必须合法，尤其是写布尔表达式
    的时候需要额外注意，尽量使⽤三⽬运算符来书
    写 jsx
-   需要注意 class 和 for 标签在书写时需要改为
    `className` 和 `htmlFor`

#### 2. create-react-app cli

官方维护的一个脚手架工具，利用下面命令可以快速创建应用。

```cmd
npx create-react-app my-app
cd my-app
npm start
```

#### 3. 函数组件和 Class 组件/受控组件和⾮受控组件

区别于要不要维护状态或生命周期。
~~`setState` 更新自身和子组件，触发 UI 的重新更新。
大部分时候，用受控组件，自己 `setValue`。~~

#### 4. 生命周期

#### 5. 常⻅错误和性能问题

## 补充知识点

待更新...

> 参考资料：
>
> -   [React 中文官网](https://zh-hans.reactjs.org/)
