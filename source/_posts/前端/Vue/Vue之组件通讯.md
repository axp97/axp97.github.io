---
title: Vue.js之组件间通信
date: 2019-09-29 18:00:00
author: 安小盼
categories: 前端
tags:
  - Vue
---

`Vue` 组件间的通信是 `Vue` 开发中很基础也十分重要的部分，写一个 Vue 工程，实际是在写一个个的组件。下面根据场景介绍组件间常用的几种通信方式~ 文章只贴出重点代码，详细代码戳demo链接~ 戳戳戳~
## 父子组件通信
### props 和 $emits

**场景1：父组件给子组件取名字（子组件的‘name’为父组件传递）**

```html
<div id="Dad">
        <Son name="subcomponent"></Son>
</div>
``` 
```html
<div id="Son">
        I am a {{name}}
</div>
props: ['name']
```
`props` 可以是数组或对象，为对象时允许配置高级选项，如类型检测、自定义验证和设置默认值。
[prop更详细信息戳这里](https://cn.vuejs.org/v2/guide/components-props.html)

* * *

**场景2：子组件召唤父组吃饭啦（子组件的通过$emit传递事件给父组件）**
```html
<div id="Dad">
        <Son @calldad="receivedMsg"></Son>
</div>
``` 
```html
<div id="Son">
        <button @click="$emit('calldad', 'Dad,开饭拉！')">callDad</button>
</div>
```
[戳demo看效果](https://codepen.io/axp97/pen/ZEzqJPa)

> **总结**：父组件通过 `prop` 给子组件下发数据，子组件通过 `$emit` 触发事件给父组件发送消息，即 `prop` 向下传递，事件向上传递。

## 三层组件通信
### \$attrs 和 $listeners
**场景1：爷爷组件给孙组件礼物（孙组件的‘gift’为爷爷组件传递）**

这里孙组件要获取搭配爷爷组件传递过来的值，可以用 **props逐级传递** ，也可以用Vue2.4.0新增的 `$attrs` 属性。简单讲的：`$attrs` 包含所有的父作用域的绑定（除了prop传递的属性、class 和 style ）。在这个场景里，给孙组件上绑定`$attrs`，获得其父的绑定，可以简便实现。
[$attrs官方介绍戳这里](https://cn.vuejs.org/v2/api/#vm-attrs)
```html
<div id="Dad">
        <Son :gift="gift"></Son>
</div>
data() {
    return {
      gift: "A lot of candy"
    };
},
``` 
```html
<div id="Son">
        <Grandson v-bind="$attrs"></Grandson>
</div>
``` 
```html
<div id="Grandson">
        <p>Gifts from the Grandpa component: <i>{{gift}}</p>
</div>
props: ["gift"]
```

**场景2：孙组件召唤爷爷组件吃饭啦（孙组件触发爷爷组件事件）**

这里孙组件要触发爷爷组件事件，可以用 **\$emit逐级触发** ，也可以用Vue2.4.0新增的 `$listeners` 属性。`$listeners` 包含了父作用域中的 (不含 .native 修饰器的) v-on 事件监听器。
[$listeners官方介绍戳这里](https://cn.vuejs.org/v2/api/#vm-listeners)

```html
<div id="Dad">
        <Son :gift="gift" @callgrandpa="callgrandpa"></Son>
</div>
methods: {
        callgrandpa(msg) {
                alert(msg);
        }
  }
``` 
```html
<div id="Son">
        <Grandson v-on="$listeners"></Grandson>
</div>
``` 
```html
<div id="Grandson">
        <button @click="onClick">callGrandpa</button>
</div>
methods: {
        onClick() {
                this.$emit("callgrandpa", "Grandpa, dinner pull!");
        }
}
```
[戳demo看效果](https://codepen.io/axp97/pen/RwbObQY)

> **总结**：三级组件通信时，当爷爷组件需要与孙组件通信，使用`$attrs`和`$listeners`更简洁。

## 多级组件通信
### EventBus
`EventBus` 通过新建一个 `Vue` 事件 `bus` 对象，然后通过 `bus.$emit` 触发事件，`bus.$on` 监听触发的事件。
```javascript
// event-bus.js
import Vue from 'vue'
export const EventBus = new Vue()
``` 
```javascript
// send messages
EventBus.$emit(channel: string, callback(payload1,…))
// listen to receive messages
EventBus.$on(channel: string, callback(payload1,…))
``` 
### Vuex
当我们需要构建一个中大型单页应用，您很可能会考虑如何更好地在组件外部管理状态，Vuex 将会成为自然而然的选择。
[戳Vuex官网链接](https://vuex.vuejs.org/zh/)

> **总结**：EventBus 适用于小型简单项目，中大型项目用Vuex管理，当项目过于庞大, 可将 store 分割成模块。

