---
title: ESNext规范
date: 2021-04-25 09:00:00
author: 安小盼
categories: 前端
tags:
    - ECMAScript
    - ES6
    - ESNext
    - 前端进阶
---

## 目标

-   了解 ECMAScript 规范发展
-   熟悉 ES6 及以后新增的常用 API

## 知识要点

### 一、 ECMAScript 规范发展

可以版本号描述，比如ES6、ES7。也可以年份来描述，比如ES2015、ES2016。

* ES6：指 2015 年 6 月发布的 ES2015 标准, 但是很多人在谈及 ES6 的时候, 都会把 ES2016、ES2017 等标准的内容也带进去。

* ESNext：泛指, 它永远指向下一个版本，如当前最新版本是 ES2020, 那么 ESNext 指的就是 2021 年 6 月将要发布的标准。

### 二、 ES6 及以后新增的常用 API

#### 1. let 和 const

* 引入了块级作用域的概念
* 暂时性死区：不允许变量提升

let 块级作用域经典题:

```js
for(var i=0; i<=3; i++){ 
    setTimeout(function() {  
        console.log(i)  
    }, 10);
} // 4~4

for(var i = 0; i <=3; i++) {
    (function (i) {
        setTimeout(function () {
            console.log(i);
        }, 10);
    })(i);
}// 0~3

for(let i=0; i<=3; i++){ 
    setTimeout(function() {  
        console.log(i)  
    }, 10);
} // 0~3，let为块级作用域
```

let 不允许变量提升体现:

```js
console.log(i); // undefined
var i = 1;

console.log(letI); // Uncaught ReferenceError: letI is not defined
let letI = 2;
```

#### 2. 箭头函数

* **箭头函数不会创建自己的 this**
    它从自己的作用域链的上一层继承 this [箭头函数资料 - MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
* 箭头函数不能用作构造函数
    构造函数会改变 this 指向到新实例出来的对象，而箭头函数的 this 在定义时已经决定了

this 区别的体现的简单栗子：

```js
const obj = {
  id: 'OBJ',
  a: function(){
    console.log(this.id);
  },
  b: () => {
    console.log(this.id);
  }
};

obj.a(); // OBJ
obj.b(); // undefined，箭头函数定义时所处的外层执行环境的this，为 window
```

这里要注意，定义对象的大括号 {} 是无法形成一个单独的执行环境的，它依旧是处于全局执行环境中！！

#### 3. Class

* constructor：构造函数
* 可以使用 set 和 get 函数
* static 为全局函数
* 直接使用变量即为类变量，无需声明

#### 4. 模板字符串

支持变量和对象解析和换行

```js
const b = 'ann'
const a  = `${b} - xxxx`;
const c = `我是换行
我换行了！
我又换行了！
`;
```

编写 render 函数：实现 template render 功能。

```js
// 条件
const year = '2021'; 
const month = '10'; 
const day = '01'; 

let template = '${year}-${month}-${day}';
let context = { year, month, day }; // { year: 2021, month: 10, day: 01 }

const str = render(template)({year,month,day}); 

console.log(str) // 2021-10-01

// 高阶函数实现：模板字符串功能
function render(template) {
    return function(context) {
        return template.replace(/\$\{(.*?)\}/g, (match, key) => context[key]);
    }
}
```

参考资料：[replace资料 - MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/replace)

#### 5. 解构

##### 5.1. 数组的解构

```js
// 基础类型解构
let [a, b, c] = [1, 2, 3]
console.log(a, b, c) // 1, 2, 3

// 对象数组解构
let [a, b, c] = [{name: '1'}, {name: '2'}, {name: '3'}]
console.log(a, b, c) // {name: '1'}, {name: '2'}, {name: '3'}

// ...解构
let [head, ...tail] = [1, 2, 3, 4]
console.log(head, tail) // 1, [2, 3, 4]

// 嵌套解构
let [a, [b], d] = [1, [2, 3], 4]
console.log(a, b, d) // 1, 2, 4

// 解构不成功为undefined
let [a, b, c] = [1]
console.log(a, b, c) // 1, undefined, undefined

// 解构默认赋值
let [a = 1, b = 2] = [3]
console.log(a, b) // 3, 2
```

##### 5.2. 对象的结构

```js
// 对象属性解构
let { f1, f2 } = { f1: 'test1', f2: 'test2' }
console.log(f1, f2) // test1, test2

// 可以不按照顺序，这是数组解构和对象解构的区别之一
let { f2, f1 } = { f1: 'test1', f2: 'test2' }
console.log(f1, f2) // test1, test2

// 解构对象重命名
let { f1: rename, f2 } = { f1: 'test1', f2: 'test2' }
console.log(rename, f2) // test1, test2

// 嵌套解构
let { f1: {f11}} = { f1: { f11: 'test11', f12: 'test12' } }
console.log(f11) // test11

// 默认值
let { f1 = 'test1', f2: rename = 'test2' } = { f1: 'current1', f2: 'current2'}
console.log(f1, rename) // current1, current2
```

##### 5.3. 解构原理

针对可迭代对象的 Iterator 接口，通过遍历器按顺序获取对应的值进行赋值。

**5.3.1 Iterator 是什么？**

一种接口/interface，为各种不一样的数据解构提供统一的访问机制。任何数据解构只要有 Iterator 接口，就能通过遍历操作，依次按顺序处理数据结构内所有成员。

使用for of的语法遍历数据结构时，自动寻找Iterator接口。

**5.3.2 Iterator 有什么用？**

* 为各种数据解构提供统一的访问接口
* 使得数据解构能按次序排列处理
* 可以使用ES6最新命令 for of进行遍历

**5.3.3 可迭代对象是什么？**

可迭代对象是 Iterator 接口的实现，有两个协议：可迭代协议和迭代器协议。

* 可迭代协议：对象必须实现 iterator 方法，即对象或其原型链上必须有一个名叫 Symbol.iterator 的属性，该属性的值为无参函数，函数返回迭代器协议。
* 迭代器协议：产生一个有限或无限序列值，必须实现 next() 方法，方法返回对象有 done 和 value 属性。

**5.3.4 实现一个可以 for of 遍历的对象**

```js
const obj = {
    count: 0,
    [Symbol.iterator]: () => {
        return {
            next: () => {
                obj.count++;
                if (obj.count <= 10) {
                    return {
                        value: obj.count,
                        done: false
                    }
                } else {
                    return {
                        value: undefined,
                        done: true
                    }
                }
            }
        }
    }
}

for (const item of obj) {
    console.log(item)
}
 
```

或者

```js
const iterable = {
    0: 'a',
    1: 'b',
    2: 'c',
    length: 3,
    [Symbol.iterator]: Array.prototype[Symbol.iterator],
};

for (const item of iterable) {
    console.log(item);
}
```

#### 6. 遍历

##### 6.1. for in

* 以任意顺序遍历一个对象的除 Symbol 以外的可枚举属性。
* 不适合遍历数组，主要应用于遍历对象
    1. 数组对象的所有自己的和继承的属性都会被迭代
    2. for..in 不能保证保持元素顺序。
    3. 执行慢：需遍历对象属性链
* 可与 break，continue，return 配合

```js
let obj = {a: 'test1', b: 'test2'}
for (let key in obj) {
    console.log(key, obj[key])
} //a test1, b test2
```
##### 6.1. for of

* 遍历可迭代对象，包括 Array，Map，Set，String，arguments 对象，NodeList 对象
* 仅遍历当前对象
* 可与 break，continue，return 配合

#### 7. Object

##### 7.1. Object.keys

返回一个给定对象的自身可枚举属性组成的数组。

```js
const obj = { a: 1, b: 2 };
const keys = Object.keys(obj); // [a, b]
```

##### 7.2. Object.values

返回一个给定对象自身的所有可枚举属性值的数组。

```js
const obj = { a: 1, b: 2 };
const keys = Object.keys(obj); // [1, 2]
```

##### 7.3. Object.entries

返回一个给定对象自身可枚举属性的键值对数组。

```js
const obj = { a: 1, b: 2 };
const keys = Object.entries(obj); // [ [ 'a', 1 ], [ 'b', 2 ] ]
```

手写实现一个函数模拟Object.entries?

```js
function getObjectEntries(obj) {
    const result = [];
    for (const prop in obj) {
        if (obj.hasOwnProperty(prop)) {
            result.push([prop, obj[prop]]);
        }
    }

    return result;
}

console.log(getObjectEntries({
    a: 1,
    b: 2
}))
```

##### 7.4. Object.getOwnPropertyNames

返回一个数组，该数组对元素是 obj 自身拥有的枚举或不可枚举属性名称字符串。

```js
Object.prototype.ann = '1111';

const testData = {
    a: 1,
    b: 2
}

for (const key in testData) {
    console.log(key); // "a", "b", "ann"
}

console.log(Object.getOwnPropertyNames(testData)) // ["a", "b"]
```

##### 7.5. Object.getOwnPropertyDescriptor

返回指定对象上一个自有属性对应的属性描述符。对象的每个属性都有一个描述对象（Descriptor），用来控制该属性的行为。

举个栗子：

```js
let obj = { foo: 123 };
Object.getOwnPropertyDescriptor(obj, 'foo')
//  {
//    value: 123,
//    writable: true, //可写，修改属性值
//    enumerable: true, //可被for in遍历    
//    configurable: true //删除属性或修改属性特性
//  }
```

再看个栗子：

```js
const object1 = {};
Object.defineProperty(object1, 'p1', {
  value: 'ann',
  writable: false
});

object1.p1 = 'not ann';

console.log(object1.p1); // 'ann'
```

> getOwnPropertyDescriptors：
> 返回所指定对象的所有自身属性的描述符，如果没有任何自身属性，则返回空对象。

##### 7.6. Object.create

创建一个新对象，两个参数：

* 第一个参数作为新对象的 [[proto]] 属性的值，根据已有对象作为原型，创建新对象
* 第二个参数为可选对象，每个属性都会作为新对象的自身属性，对象的属性值以 descriptor 的形式出现，且 enumerable 默认为 false

```js
const b = Object.create(null) // 返回纯{}对象，无prototype；创建一个纯净的对象

b // {}
b.__proto__ // undefined
b.toString() // throw error
```

##### 7.6. Object.defineProperty

为 ES5 属性，也扩展一下。

用于在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回此对象。

```js
Object.defineProperty(obj, prop, descriptor) // 对象，属性名称，描述符对象
```

> 扩展 - object 属性：object 包括属性和方法，其中属性分为数据属性和访问器属性
> 
> * 数据属性
>
>   * configurable：通过 delete 删除并重新定义属性，是否可修改属性的特性，已经是否可把它改为访问器属性，默认 true。
>   * enumerable：是否可通过 for-in 循环，默认 true。
>   * writable：属性值是否可修改，默认 true。
>   * value：属性实际的值，默认为 undefined
> * 访问器属性
> 
>   * configurable
>   * enumerable
>   * get：获取函数，在读取属性时调用，默认 undefined
>   * set：设置函数，在写入属性时调用，默认 undefined


#### 8. Array

##### 8.1. Array.flat

按照一个可指定的深度递归遍历数组，并将所有元素与遍历到的子数组中的元素合并为一个新数组返回。

* depth 可选
要提取嵌套数组的结构深度，默认值为 1

`let newArray = arr.flat([depth]); `

##### 8.2. Array.includes

判断一个数组是否包含一个指定的值，返回 boolean。

* valueToFind
需要查找的元素值。

* fromIndex 可选
开始查找的索引，默认为 0。

`arr.includes(valueToFind[, fromIndex])`

##### 8.3. Array.find

返回数组中满足提供的测试函数的第一个元素的值，否则返回 undefined。

`arr.find(callback(element, index, arr)[, thisArg])`

##### 8.4. Array.from

从一个类似数组或可迭代对象创建一个新的，浅拷贝的数组实例。

* arrayLike
想要转换成数组的伪数组对象或可迭代对象。
* mapFn 可选
如果指定了该参数，新数组中的每个元素会执行该回调函数。

`Array.from(arrayLike[, mapFn[, thisArg]])`

##### 8.5. Array.of

创建一个具有可变数量参数的新数组实例，而不考虑参数的数量或类型。

`Array.of(element0[, element1[, ...[, elementN]]])`

#### 9. Proxy 和 Reflect

##### 9.1. Proxy

用于创建一个对象的代理，从而实现基本操作的拦截和自定义（如属性查找、赋值、枚举、函数调用等）。

* 定义： const proxy = new Proxy(target, handler)，target代理对象，handle捕获器
* handle 参数 get：trapTarget(目标对象)，property(代理属性)，receiver(代理对象)
* handle 参数 set：trapTarget(目标对象)，property(代理属性)，value(赋给属性的值)，receiver(代理对象)

```js
const obj = new Proxy({}, {
    get: function (target, propKey, receiver) {
        console.log(`getting ${propKey}`);
        return target[propKey];
    },
    set: function (target, propKey, value, receiver) {
        console.log(`setting ${propKey}`);
        return Reflect.set(target, propKey, value, receiver);
    }
});

obj.something = 1;
console.log(obj.something);
```

##### 9.2. Reflect

Reflect 是一个内置的对象，它提供拦截 JavaScript 操作的方法。

* 将 Object 对象上的明显属于语言内部的方法，放在 Reflect 对象上
* 让 Object 操作变为函数行为，eg：name in obj 和 delete，变为 Reflect.has(obj,name) 和 Reflect.deleteProperty(obj, name)
* Reflect 对象的方法与 Proxy 对象的方法一一对应。让 Proxy 对象可以方便的调用对应的 Reflect 方法，完成默认行为。
* 通过 defineProperty 设置 writable 为 false 的对象，不能使用 Proxy。