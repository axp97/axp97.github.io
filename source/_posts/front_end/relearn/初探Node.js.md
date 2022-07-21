---
title: 初探Node.js
date: 2021-08-01 22:00:00
author: 安小盼
categories: 前端
tags:
  - Node.js
  - 前端进阶
---

## 目标

- [x] `Node.js` 基础
- [x] `CommonJS` 模块化规范及原理✨
- [x] 实现简易版 `CommonJS`

## 知识要点

### 一、 Node.js简介

`node.js` 是⼀个 `JS` 的**服务端运⾏环境**，`JS` 只能进⾏⼀些简单的逻辑运算，`node.js` 基于 `JS` 语法增加**与操作系统之间的交互**。👍

#### 1. 安装

* 通过官⽹的**⼆进制安装⽂件**来安装
    * 适用：个人开发电脑
    * node.js 本质上也是软件
* 通过**二进制编译方式**来安装
    * 适用：打包上线的一些nodejs环境，比如 docker、linux 不适合通过安装文件安装

#### 2. 版本切换工具

❔为什么使用呢？

* 不同项目用不同的 node 版本
* 插件对 node.js 版本的兼容问题

##### 2.1. nvm工具👍

[nvm下载的传送门](https://github.com/coreybutler/nvm-windows/releases)

**原理：** 通过 nvm 切换环境变量里 node 命令指向的软件路径

##### 2.2. n工具

n 依赖于 node，n 命令是 node 的一个模块，安装方式如下：

```cmd
npm install -g n
```

**❗注意：** n 对全局模块毫无作为，因此有可能在切换了 node 版本后发生全局模块执行出错的问题。

#### 3. 包管理工具

npm 熟悉的老大哥啦，是 node.js 内置的 ⼀款⼯具。

#### 4. 周边工具解析

略

### 二、 CommonJS模块

#### 1. 底层依赖

* V8 引擎：主要是 JS 语法的解析，有了它才能识别 JS语法
* libuv：C 语⾔实现的⼀个⾼性能异步⾮阻塞 IO 库，⽤来实现 node.js 的事件循环
* http-parser/llhttp：底层处理 http 请求，处理报⽂，解析请求包等内容
* openssl：处理加密算法，各种框架运⽤⼴泛，底层依赖，无需js实现
* zlib：处理压缩等内容

#### 2. 常⻅内置模块

node.js 中最主要的内容，就是实现了⼀套 CommonJS的模块化规范，以及内置了⼀些常⻅的模块。

* fs：⽂件系统，能够读取写⼊当前安装系统环境中硬盘的数据
* path：路径系统，能够处理路径之间的问题
* crypto：加密相关模块，能够以标准的加密⽅式对我们的内容进⾏加解密
* dns：处理 dns 相关内容，例如我们可以设置 dns 服务器等等
* http: 设置⼀个 http 服务器，发送 http 请求，监听响应等等
* readline: 读取 stdin 的⼀⾏内容，可以读取、增加、删除我们命令⾏中的内容
* os：操作系统层⾯的⼀些 api，例如告诉你当前系统类型及⼀些参数
* vm: ⼀个专⻔处理沙箱的虚拟机模块，底层主要来调⽤ v8 相关 api 进⾏代码解析

#### 1. 基础

### 三、 CommonJS模块

#### 1. 基础

#### 1.1. require 和 exports

```js
// 引入模块：
require("./index.js")
// 导出模块：
module.exports={...} 
exports.key={}
```

* 缓存
    * `require` 值会缓存，通过 `require` 引用文件时，会将文件执行一遍后，将结果通过浅克隆的方式，写入全局内存，后续 `require` 该路径，直接从内存获取，无需重新执行文件
* 值拷贝
    * 模块输出是值的拷贝，一旦输出，模块内部变化后，无法影响之前的引用，而`ESModule` 是引用拷贝。`commonJS` 运行时加载，`ESModule` 编译阶段引用
* `commonJS` 和 `ESModule`
    * `CommonJS` 在引入时是加载整个模块，生成一个对象，然后再从这个生成的对象上读取方法和属性
    * `ESModule` 不是对象，而是通过export暴露出要输出的代码块，在 `import` 时使用静态命令的方法引用制定的输出代码块，并在 `import` 语句处执行这个要输出的代码，而不是直接加载整个模块

#### 1.2. 经典面试：exports

为何不能使用 `exports` 导出 `commonjs` 模块，eg：`exports = “hello”`?

**解析：** 

* `commonjs` 模块是通过 `module.exports` 导出模块，`exports` 为 `module.exports` 的引用
* 使用`exports` 直接赋值导出模块，只会改变 `exports` 的引用不会导出 `commonjs` 模块。
* 引用断掉了，没法导出新的变量/模块

```js
// cjs正确导出用法
exports.key = 'hello world'
module.exports = "hello world"

// 错误导出
exports = "hello world" //无法输出

// 解析：
const obj = {
  key: {}
}

// 方式一：obj 引用
obj.key = "hello world"

// 方式二：赋值给变量，对象变量相同引用
const key = obj.key
key.key1 = "hello world"

/* 
无效：通过引用改变，改变了key的引用
引用断掉；无法改变obj
*/
key = "hello world"
```

#### 2. 原理实现

```js
const path = require('path')
const fs = require('fs')
const vm = require('vm')

function customRequire (filename) {
  const pathToFile = path.resolve(__dirname, filename)
  const content = fs.readFileSync(pathToFile, 'utf-8')
  
  const wrapper = [
    '(function(require, module, exports, __dirname, __filename, zhuawa) {',
    '})'
  ]
  const wrappedContent = wrapper[0] + content  + wrapper[1]
  
  const script = new vm.Script(wrappedContent, {
    filename: 'index.js'
  })
  
  const module = {
    exports: {}
  }
  
  const result = script.runInThisContext()
  // 函数执行，引入模块，若内部有 require 继续递归
  result(customRequire, module, module.exports, null, null)
  
  return module.exports
}

global.r = customRequire
```

#### 3.源码解析

源码路径：/lib/internal/modules/cjs/

```js
//loader.js
//require函数定义
1、Module.prototype.require：调用__load函数
2、Module.__load：_cache处理，调用load函数
3、Module.prototype.load函数：调用Module._extensions[extension](this, filename);
//不同的后缀通过定义不同的函数指定解析规则：以Module._extensions['.js']为例
4、Module._extensions['.js'] = function(module, filename) {
  //读取缓存或者通过readFileSync读取内容
  if (cached?.source) {
    content = cached.source;
    cached.source = undefined;
  } else {
    content = fs.readFileSync(filename, 'utf8');
  }
  //...
  //调用compile解析
  module._compile(content, filename);
}
5、Module.prototype._compile = function(content, filename){
  //生成包裹函数：warpSafe获取函数字符串并使用vm执行生成执行函数
  const compiledWrapper = wrapSafe(filename, content, this);
  //执行函数
  const exports = this.exports;
  const thisValue = exports;
  const module = this;
  if (inspectorWrapper) {
    result = inspectorWrapper(compiledWrapper, thisValue, exports,
        require, module, filename, dirname);
  } else {
    //静态方法Reflect.apply(target, thisArgument, argumentsList)
    //通过指定的参数列表发起对目标(target)函数的调用
    result = ReflectApply(compiledWrapper, thisValue,
        [exports, require, module, filename, dirname]);
  }
  return result;
}
6、function wrapSafe(filename, content, cjsModuleInstance) {
    /* 生成包裹函数字符：
    let wrap = function(script) {
       return Module.wrapper[0] + script + Module.wrapper[1];
    };
    const wrapper = [
      '(function (exports, require, module, __filename, __dirname) { ',
      '\n});',
    ]；*/
  	const wrapper = Module.wrap(content);
    //获取包裹函数
    return vm.runInThisContext(wrapper, {
      filename,
      lineOffset: 0,
      displayErrors: true,
      importModuleDynamically: async (specifier) => {
        const loader = asyncESM.ESMLoader;
        return loader.import(specifier, normalizeReferrerURL(filename));
      },
    });
}
```