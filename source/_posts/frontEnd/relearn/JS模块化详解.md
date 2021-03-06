---
title: JS模块化详解
date: 2021-04-05 22:20:00
author: 安小盼
categories: 前端
tags:
  - Javascript
  - 前端进阶
---

首先我们明确一下：编程的本质是管理变量呀。为了更好的管理代码、组织及通信的模式而出现了模块化的技术方案。

本质上模块就是一种提供对外通信接口，进行代码切分/组合的管理方式。其呈现的方式因不同的模块化方案而不同，基本是以文件粒度区分。

## 目标

**为什么学**

* 知古鉴今，单一职，关注分离
* 深入理解 JS 模块化

**能学到什么**

* 了解模块化概念
* 手写实现

## 知识要点

### JS 模块化

#### 1. 发展历史

**1.1. 函数时代**

缺点：命名冲突、全局污染

**1.2. 命名空间**

优点：函数命个名，单一职
缺点：能访问还能改

**1.3. 闭包**

现代模块化的基石
特点：私有性、可往里塞东西；依赖注入
缺点：函数通信，window 上挂

**1.4. 接口方式**

不挂全局，写个函数 return 结果的形式
现在模块化机制的基本雏形

#### 2. 类别

#####  2.1 AMD
#####  2.2 CMD
#####  2.3 AMD
#####  2.4 ESM
#####  2.5 UMD

## 补充知识点

待更新...

![xmind总结](/static/xmind/frontEnd/relearn/modular.png)
