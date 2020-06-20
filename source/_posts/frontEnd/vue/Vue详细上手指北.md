---
title: Vue详细上手指北
date: 2020-06-20 8:30:00
author: 安小盼
categories: 前端
tags:
  - Vue
---

这是篇教程，跟着我的节奏来，学完后可以独立上手开发 Vue 项目！

## 01 快速搭建项目

本节目的：用 Vue-cli 脚手架搭建一个 Vue 项目。    
[戳官网教程](https://cli.vuejs.org/zh/guide/installation.html)

## 前言

#### Vue 是什么

一套用于构建用户界面的前端 JS 框架。[戳官网详细介绍](https://cn.vuejs.org/v2/guide/)

#### 为什么用 Vue

流行：github 上 star 最多的前端框架，招聘大多要求。
学习：上手简单（数据驱动）
性能：组件化，性能好，运行速度快

#### 扩展

**Q：Vue 开发跟传统 Web 开发相比的优势？**
A：传统开发需操作 dom 元素，Vue 只关心图层，用数据驱动视图。减少额代码工作量，能更快地开发迭代项目。

* * *

## 上手

#### 1、环境配置：node.js

- [戳官网链接下载](https://nodejs.org/zh-cn/)
- 下载安装成功后可查看版本
  1.  打开命令行（Win+R，键入 cmd）输入     node -v
  2.  查看 npm 版本（node 自带安装了 npm）    npm -v
  3.  无报错，即显示了 node 版本即成功

#### 扩展

**Q：为什么用 node.js？**
A：node.js 在这里提供 npm 安装，为了方便模块管理。也可以 Web 页面中直接使用。

#### 2、安装 vue/cli 包

- 命令行输入：`npm install -g @vue/cli`
- 安装后，命令行查看版本，输入：`vue --version`

#### 3、创建项目

利用脚手架创建一个默认配置的名称为 first_vue 的项目

- 到指定文件夹打开命令行
- 命令行输入：`vue create first_vue`
  ![](/static/vue/tutorial/chapter1_1.png)
- 完成后显示
  ![](/static/vue/tutorial/chapter1_2.png)
- 命令行进入项目目录和运行项目；浏览器打开
  ![](/static/vue/tutorial/chapter1_3.png)
  ![](/static/vue/tutorial/chapter1_4.png)

## 02 下一节

下一节还没写好，客官稍安勿躁~
