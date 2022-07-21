---
title: Vue.js之状态管理
date: 2021-05-16 22:25:00
author: 安小盼
categories: 前端
tags:
  - Vue
  - Vuex
  - 状态管理
  - 前端进阶
---

## 目标

**为什么学**

* 状态管理是前端核心部分

**能学到什么**

* 熟悉状态管理的思想
* Vuex 核心原理与使用
* 手写实现简单的 Vue 状态管理

## 知识要点

### 一、状态管理

#### 1. 状态是什么

最广义程度来看，`状态` 本质是还是一种可以描述视图状态、行为的数据结构。

往细了看，状态大致可以分为两类，**本地状态**和**共享状态**。

本地状态就是 `vue` 中的 `data`，`react`中的 `state`，这里我们一般会用来控制弹窗的现实隐藏、`loading`效果等。

共享状态是项目中可以共享的数据。

#### 2. 状态管理是什么

`状态的管理` 则是通过一定的算法将这些 `状态` 组织、管理起来，又回到了 `程序=算法+数据结构` 这一基本概念。

状态管理分为中心化和去中心化两种模式。

#### 3. 理想状态管理工具

1. 状态更新的设计，`API` 足够少，且简单
2. 如何共享状态
3. 状态提升
4. 状态下降
5. 同步、异步的处理
6.  持久状态和临时状态如何区分维护
7.  状态更新的事务如何管理
8. 去中心化
9. ...

### 二、Vuex 介绍

在 Vue 项⽬中，每个组件的数据都有其独⽴的作⽤域。当组件间需要跨层级或者同层之间频繁传递的时候，数据交互就会⾮常繁琐。Vuex 的主要作⽤就是集中管理所有组件的数据和状态以及规范数据修改的⽅式。

#### 1. 核心原理

Flux 架构主要思想是**应用的状态被集中存放到一个仓库中，但是仓库中的状态不能被直接修改，必须通过特定的方式**才能更新状态。

Vuex 基于 flux 思想为 Vue 框架定制，区分同步和异步，定义两种行为，Actions 用来处理异步状态变更（内部还是调用 Mutations），Mutations 处理同步的状态变更，整个链路应该是一个闭环，单向的，完美契合 FLUX 的思想。

「页面 dispatch/commit」-> 「actions/mutations」-> 「状态变更」-> 「页面更新」-> 「页面 dispatch/commit」...

![vuex](/static/front-end/relearn/vuex.png)

#### 2. Vuex 五大核心

* `State`：单一状态树
  * 用一个对象就包含了全部的应用层级状态。
  * 至此它便作为一个“唯一数据源 (SSOT (opens new window))”而存在。
* `Getter`：`state` 的计算属性
  * 从 `store` 中的 `state` 中派生出一些状态。
  * 场景：当需要重复对某个数据进⾏某种操作的时候可以封装在 `getter` ⾥⾯，当 `state` 中的数据改变了以后对应的 `getter` 也会相应的改变
* `Mutation`：更改 `Vuex` 的 `store` 中的状态的唯一方法
  * 必须是同步函数
  * 每个 `mutation` 都有一个字符串的 事件类型 (type) 和 一个 回调函数 (handler)
  * 回调函数就是我们实际进行状态更改的地方，并且它会接受 `state` 作为第一个参数，第二个参数为载荷（`payload`）对象
* `Action`：类似于 `Mutation`
  * Action 提交的是 `mutation`，而不是直接变更状态。
  * Action 可以包含任意异步操作。
  * Action 函数接受一个 `context` 参数，它与 `store` 实例有着相同的方法和属性，可以使用 `context.commit` 来提交一个 `mutation`，或者通过 `context.state` 和 `context.getters` 来获取 `state` 和 `getters`
* `Module`：模块化分隔，防⽌ `state` 过于庞⼤和冗余
  * 模块内部的 `state` 是局部的，只属于模块本身所有，所以外部必须通过对应的模块名进行访问
  * 模块内部的 `action`、`mutation` 和 `getter` 默认可是注册在全局命名空间的，通过添加 `namespaced: true` 的方式使其成为带命名空间的模块。当模块被注册后，它的所有 `getter`、`action` 及 `mutation` 都会自动根据模块注册的路径调整命名。

<!-- #### 3. 辅助函数

* mapStates
* mapGetters
* mapMutaions
* mapActions -->


### 三、Vuex 原理

#### 1. 设计思路

**1.1. FLUX 思想**

* 问题：在开发中面临最多的场景是状态重复但是不集中，在不同的组件中依赖了同样的状态，重复就会导致不对等的风险。
* 思想：基于 `FLUX` 的思想，我们设计的状态管理将是 `中心化` 的工具，也就是集中式存储管理应用的所有组件的状态，将所有的状态放在一个全局的 Tree 结构中，集中放在一起的好处是可以有效避免重复的问题，也更好的管理，将状态和视图层解耦。
* 解决：使用全局的 store 对象管理状态和数据，单一状态树

**1.2. 状态流转**

* 单一流转
* 同步和异步分层：`Mutations` 负责同步状态管理、`Actions` 负责异步事件（内部通过mutations改变状态）。

**1.3. 与 Vue 集成**

* 通过插件将 Vue 集成在一起
* 通过 mixin 将 $store 这样的快速访问 store 的快捷属性注入到每一个 Vue 实例中

**1.4. 响应式**

* 利用 Vue 的 data 是响应式实现

**1.5. 扩展**

* 辅助函数
* 模块化
* 插件支持

#### 2. 简单实现

**2.1. store 注册**

```js
let Vue

// vue 插件必须要这个 install 函数
export function install(_Vue) {
  // 拿到 Vue 的构造器，存起来
  Vue = _Vue
  // 通过 mixin 注入到每一个vue实例
  Vue.mixin({ beforeCreate: vuexInit })
  
  function vuexInit () {
    const options = this.$options
    // 这样就可以通过 this.$store 访问到 Vuex 实例，拿到 store 了
    if (options.store) {
      this.$store = typeof options.store === 'function'
        ? options.store()
        : options.store
    } else if (options.parent && options.parent.$store) {
      this.$store = options.parent.$store
    }
  }
}
```

**2.2. 实现响应式**

```js
export class Store {
  constructor(options = {}) {
    resetStoreVM(this, options.state)
  }
  
  get state () {
    return this._vm._data.$$state
  }
}

function resetStoreVM(store, state) {
  // 因为 vue 实例的 data 是响应式的，正好利用这一点，就可以实现 state 的响应式
  store._vm = new Vue({
    data: {
      $$state: state
    }
  })
}
```

**2.3. 衍生数据（getters）**

```js
export class Store {
  constructor(options = {}) {
    
    const state = options.state
    
    resetStoreVM(this, state)
    
    // 我们用 getters 来收集衍生数据 computed
    this.getters = {}
    
    // 简单处理一下，衍生不就是计算一下嘛，传入 state
    _.forEach(this.getters, (name, getterFn) => {
      Object.defineProperty(this.getters, name, {
        get: () => getterFn(this.state)
      })
    })
  }
  
  get state () {
    return this._vm._data.$$state
  }
}

function resetStoreVM(store, state) {
  store._vm = new Vue({
    data: {
      $$state: state
    }
  })
}
```

**2.4. Actions/Mutations**

```js
export class Store {
  constructor(options = {}) {
    
    const state = options.state
    
    resetStoreVM(this, state)
    
    this.getters = {}
    
    _.forEach(options.getters, (name, getterFn) => {
      Object.defineProperty(this.getters, name, {
        get: () => getterFn(this.state)
      })
    })
    
    // 定义的行为，分别对应异步和同步行为处理
    this.actions = {}
    this.mutations = {}
    
    _.forEach(options.mutations, (name, mutation) => {
      this.mutations[name] = payload => {
        // 最终执行的就是 this._vm_data.$$state.xxx = xxx 这种操作
        mutation(this.state, payload)
      }
    })
    
    _.forEach(options.actions, (name, action) => {
      this.actions[name] = payload => {
        // action 专注于处理异步，这里传入 this，这样就可以在异步里面通过 commit 触发 mutation 同步数据变化了
        action(this, payload)
      }
    })
  }
  
  // 触发 mutation 的方式固定是 commit
  commit(type, payload) {
    this.mutations[type](payload)
  }
  
  // 触发 action 的方式固定是 dispatch
  dispatch(type, payload) {
    this.actions[type](payload)
  }
  
  get state () {
    return this._vm._data.$$state
  }
}

function resetStoreVM(store, state) {
  store._vm = new Vue({
    data: {
      $$state: state
    }
  })
}
```

**2.5. 分形，拆分出多个 Module**

```js
// module 可以对状态模型进行分层，每个 module 又含有自己的 state、getters、actions 等

// 定义一个 module 基类
class Module {
	constructor(rawModule) {
    this.state = rawModule || {}
    this._rawModule = rawModule
    this._children = {}
  }
  
  getChild (key) {
    return this._children[key]
  }
  
   addChild (key, module) {
    this._children[key] = module
  }
}

// module-collection.js 把 module 收集起来
class ModuleCollection {
  constructor(options = {}) {
    this.register([], options)
  }
  
  register(path, rawModule) {
    const newModule = new Module(rawModule)
    if (path.length === 0 ) {
      // 如果是根模块 将这个模块挂在到根实例上
      this.root = newModule
    }
    else {
      const parent = path.slice(0, -1).reduce((module, key) => {
        return module.getChild(key)
      }, this.root)
      
      parent.addChild(path[path.length - 1], newModule)
    }
    
    // 如果有 modules，开始递归注册一波
    if (rawModule.modules) {
      _.forEach(rawModule.modules, (key, rawChildModule) => {
        this.register(path.concat(key), rawChildModule)
      })
    }
  }
}

// store.js 中
export class Store {
  constructor(options = {}) {
    // 其余代码...
    
    // 所有的 modules 注册进来
    this._modules = new ModuleCollection(options)
    
    // 但是这些 modules 中的 actions, mutations, getters 都没有注册，所以我们原来的方法要重新写一下
    // 递归的去注册一下就行了，这里抽离一个方法出来实现
    installModule(this, this.state, [], this._modules.root);
  }
}

function installModule(store, state, path, root) {
  // getters
  const getters = root._rawModule.getters
  if (getters) {
    _.forEach(getters, (name, getterFn) => {
      Object.defineProperty(store.getters, name, {
        get: () => getterFn(root.state)
      })
    })
  }
  
  // mutations
  const mutations = root._rawModule.mutations
  if (mutations) {
    _.forEach(mutations, (name, mutation) => {
      let _mutations = store.mutations[name] || (store.mutations[name] = [])
      _mutations.push(payload => {
        mutation(root.state, payload)
      })
      
      store.mutations[name] = _mutations
    })
  }
  
  // actions
  const actions = root._rawModule.actions
  if (actions) {
    _.forEach(actions, (name, action) => {
      let _actions = store.actions[name] || (store.actions[name] = [])
      _actions.push(payload => {
        action(store, payload)
      })
      
      store.actions[name] = _actions
    })
  }
  
  // 递归
  _.forEach(root._children, (name, childModule) => {
    installModule(this, this.state, path.concat(name), childModule)
  })
}
```

**2.6. 插件机制**

```js
(options.plugins || []).forEach(plugin => plugin(this))
```


> 参考资料
>
> * [Vuex 官方资料](https://vuex.vuejs.org/zh/guide/)
> * [Vuex 源代码地址](https://github.com/vuejs/vuex/tree/dev/src)
> * [vue-vuex 最佳实践](https://github.com/weipxiu/Vue-vuex)
> * [vuex 最佳实践](https://bigdata.bihell.com/language/javascript/vue/vuex.html#%E4%B8%80%E3%80%81vuex%E5%88%B0%E5%BA%95%E6%98%AF%E4%B8%AA%E4%BB%80%E4%B9%88%E9%AC%BC)
> * [学习 vuex 源码整体架构，打造属于自己的状态管理库](https://juejin.cn/post/6844904001192853511#heading-12)
> * [开发 Vue 插件](https://cn.vuejs.org/v2/guide/plugins.html)

![xmind总结](/static/xmind/front_end/relearn/vuex.png)
