---
title: 前端模块化
date: 2021-04-05 22:20:00
author: 安小盼
categories: 前端
tags:
  - 前端工程化
  - 前端模块化
  - 前端进阶
---

首先我们明确一下：编程的本质是管理变量呀。为了更好的管理代码、组织及通信的模式而出现了模块化的技术方案。

## 目标

**为什么学**

* 知古鉴今，单一职，关注分离
* 深入理解 JS 模块化

**能学到什么**

* 了解模块化概念
* 手写实现

## 知识要点

### 一、简介

本质上模块就是一种提供对外通信接口，进行代码切分/组合的管理方式。其呈现的方式因不同的模块化方案而不同，基本是以文件粒度区分。

#### 1. 作用

* 抽取公共代码
* 隔离作用域
* 依赖管理

#### 2. 发展历史

**2.1. 函数时代**

缺点：命名冲突、全局污染

**2.2. 命名空间**

优点：函数命个名，单一职
缺点：能访问还能改

**2.3. 闭包**

现代模块化的基石
特点：私有性、可往里塞东西；依赖注入
缺点：函数通信，window 上挂

**2.4. 接口方式**

不挂全局，写个函数 return 结果的形式
现在模块化机制的基本雏形

### 二、类别

历史：无模块化(IIFE) -> CommonJS -> AMD -> CMD -> ESModule，特殊UMD

####  1 AMD (Asynchronous Modules Definition)

* 异步模块定义，浏览器端运行，所有模块默认都是异步加载
* 代表技术实现 [RequireJS](https://requirejs.org/docs/api.html)
* 使用：定义 `define`、加载 `require` 、配置 `config`

####  2 CMD (Common Module Definition)

* 通用模块定义
* 代表人物玉伯，代表技术实现 Seajs

####  3 CMJ(CommonJS)


####  4 ESM

* 服务端和浏览器端通用，ESModule 由 JS 解释器实现，CMJ 和 AMD 是在宿主环境中运行时实现
* `import` 输入、`export` 输出

####  5 UMD

组合：AMD 和 CommonJS

## 补充知识点

待更新...

![xmind总结](/static/xmind/front_end/relearn/modular.png)
