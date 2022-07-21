---
title: Vue原理之响应式设计
date: 2021-05-31 8:00:00
author: 安小盼
categories: 前端
tags:
    - Vue
    - 响应式设计
    - 前端进阶
---

## 目标

**为什么学**

-   响应式是 Vue 最独特的特性之一

**能学到什么**

-   深入响应式原理
-   手撸 Vue 响应式原理

## 知识要点

### 一、 原理总结

我们都知道，只要在 Vue 实例中声明过的数据，那么这个数据就是响应式的。也就是当你修改数据时，视图会进行更新。这是我们说的响应式。

`Vue2.x` 通过 `Object.defineProperty()` 做数据劫持，而 `Vue3` 通过 `Proxy` 做数据代理，从而捕捉到对数据的 `get` 和 `set` 。

本质的思想都是在 `getter` 时候收集副作用，在 `setter` 时候触发副作用执行。

### 二、 Vue2 响应式实现

<!-- #### 1. 整体结构

申明整体核心类及方法，确认整体框架结构：

* index.html 主页面
* vue.js Vue主文件
* compiler.js 编译模版，解析指令（v-model等）
* dep.js 收集依赖关系，存储观察者，以发布订阅模式实现
* observer.js 实现数据劫持
* watcher.js 观察者对象类 -->

![Vue2响应式原理](/static/front-end/relearn/reactive-vue2.png)

待更新...