---
title: 初探TypeScript
date: 2021-04-12 20:00:00
author: 安小盼
categories: 前端
tags:
  - Javascript
  - 前端进阶
---

## 目标

* 熟悉 ts 
* 面试题及实战
* 了解 ts 基本原理

## 知识要点


### 0. 准备

```
npm install -g typescript
tsc hello.ts
```

TypeScript 基础方面总结请跳到文底看 xmind 文件呢，或者阅览[官方文档](https://www.typescriptlang.org/)。
贴心准备：[TypeScript中文网](https://www.tslang.cn/docs/handbook/typescript-in-5-minutes.html)

### 1. type 和 interface 的异同

用interface描述数据结构，用type描述类型

**相同点**

* 都可以描述一个对象或者函数
* 都允许扩展 extends：interface 和 type 都可以拓展，并且两者并不是相互独立的，也就是说 interface 可以 extends type, type 也可以 extends interface ，语法不同。

**不同点**

* type 可以声明基本类型别名，联合类型，元组等类型
* type 语句中还可以使用typeof获取实例的类型进行赋值
* interface 能够声明合并

  ```js
  // 1. 都可以描述一个对象或者函数
  interface User {
    name: string
    age: number
  }

  interface SetUser {
    (name: string, age: number): void
  }

  type User = {
    name: string
    age: number
  }

  type SetUser = (name: string, age: number) => void;

  // 2. 都允许扩展
  // 2.1 interface extends interface
  interface Name {
    name: string
  }

  interface User extends Name {
    age: number
  }

  // 2.2 type extends type
  type Name = {
    name: string
  }

  type User = Name & { age: number }

  // 2.3 interface extends type

  type Name = {
    name: string
  }

  interface User extends Name {
    age: number
  }

  // 2.4 type extends interface

  interface Name {
    name: string
  }

  type User = Name & {
    age: number
  }

  // 3. 只有 type 可以做的
  // 3.1 type 声明基本类型别名
  type Name = string

  // 3.2 type 声明联合类型
  interface Dog {
    wang();
  }

  interface Cat {
    miao();
  }

  type Pet = Dog | Cat

  // 3.2 type 声明元组类型，具体定义数组每个位置的类型
  type PetList = [Dog, Pet]

  // 4. 当你想获取一个变量的类型时，使用 typeof
  let div = document.creatElement('div');
  type B = typeof div

  ```

### 2. 如何基于一个已有类型, 扩展出一个大部分内容相似, 但是有部分区别的类型?

  可以通过 Pick 和 Omit

  ```ts
  interface Test {
    name: string
    sex: number
    height: string
  }

  type Sex = Pick<Test, 'sex'>

  const a: Sex = { sex: 0 }

  type WithoutSex = Omit<Test, 'sex'>

  const b: WithoutSex = { name: 'Axp', height: '158cm' }

  ```

  ### 3. 什么是泛型, 泛型的具体使用?

  泛型是指在定义函数、接口或类的时候，不预先指定具体的类型，使用时再去指定类型的一种特性。

  可以把泛型理解为代表类型的参数


![xmind总结](/static/xmind/frontEnd/relearn/ts.png)
