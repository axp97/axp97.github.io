---
title: Promise规范及应用
date: 2021-04-04 15:15:00
author: 安小盼
categories: 前端
tags:
    - Javascript
    - 前端进阶
---

## 目标

**为什么学**

-   Promise 重要性：async/await 语法的基础，JavaScript 中处理异步的标准形式。
-   深入理解 Promises 运行机制

**能学到什么**

-   解读 Promises/A+ 规范
-   实现一个 Promise
-   简介 Generator 和 Async

## 知识要点

### 一、 解读 Promise/A+规范

[规范内容](https://promisesaplus.com/)
[【翻译】Promises/A+规范](https://www.ituring.com.cn/article/66566)

#### 1. 了解术语

-   `promise` 是一个包含 then 方法的对象或函数，该方法符合规范指定的行为。
-   `thenable` 是一个包含 then 方法和对象或者函数。
-   `value` 就是任意合法 JS 值。
-   `exception` 就是 throw 语句抛出的值。
-   `reason` 是一个指示 promise 为什么被 rejected 的值。

#### 2. Promise 状态

-   **pending**
    初始状态，可改变
    在 resolve 和 reject 前都处于这个状态

        ```
        --> resolve(value) --> fulfilled
        --> reject(reason) --> rejected
        ```

-   **fulfilled**
    一次性特征：最终态，不可变
    必须拥有一个 value 值

-   **rejected**
    最终态，不可变
    必须拥有一个 reason

#### 3. Then 方法

promise 必须有 then 方法，接受 `onFulfilled` 和 `onRejected` 参数。

##### 3.1. 参数规范

-   函数：必须最多执行一次
-   非函数：忽略，返回默认值/函数

##### 3.2. 参数特性

微任务阶段执行，执行时机：

-   promise --> fulfilled/rejected 时，调用 onFulFilled(value) / onRejected(reson)
-   在 promise 变成 fulfilled/rejected 之前，不应该调用 onFulfilled/onRejected
-   只能调用一次

#### 4. then 方法可以调用多次

执行时机：

promise --> fulfilled / rejected 后，按照注册的顺序执行

#### 5. 返回值：promise

若返回非函数：

promise2 以 promise1 的 value/reson 触发 fulfilled

返回函数：

onFulfilled / onRejected 执行，过程中抛出 e，promise2 需要 reject

结果为 x，调用 `resolvePromise`

#### 6. resolvePromise(promise2, x, resolve, reject)

##### 6.1 参数

-   promise2：当前第一个 promise 的返回
-   x：onFulfilled/onRejected 执行结果

##### 6.2 规范

**1. 避免死循环**

```javascript
if (promise2 === x)｛
    reject typeError
｝
```

**2. type x**

-   promise，取决于 x 的状态
    pending：promise 必须在 pending 状态，直到 x 的状态变更
    fulfilled：value -> fulfilled
    rejected：reason -> rejected

-   Object || Function
    `const then = x.then`

如果报错，reject reson

-   非函数：resolve(x)
-   函数：then.call(x, resolvePromiseFn, rejectPromiseFn)

### 二、 根据 Promises/A+ 规范实现一个 Promise

#### 1. 试着实现

**0. 公共方法**

  ```js
  isFunction(param) {
    return typeof param === 'function';
  }
  ```

**1. 定义三种状态，设置初始态，用 Class 实现**

  ```js
  const PENDING = 'pending';
  const FULFILLED = 'fulfilled';
  const REJECTED = 'rejected';

  class MPromise {
    constructor() {
      this.status = PENDING;
      this.value = null;
      this.reason = null;
    }
  }
  ```

**2. 添加 reject 和 resolve 方法更改状态**

  ```js
  resolve(value) {
    if (this.status === PENDING) {
      // 顺序注意一下: fulFilledFnWithCatch 用到
      this.value = value;
      this.status = FULFILLED;
    }
  }

  reject(reason) {
    if (this.status === PENDING) {
      this.reason = reason;
      this.status = REJECTED;
    }
  }
  ```

**3. 添加 Promise 入参，函数同步执行，异常需reject**

  ```js
  constructor(fn) {
    this.status = PENDING;
    this.value = null;
    this.reason = null;

    try {
      // bind: 改变 this 指向，返回一个函数
      fn(this.resolve.bind(this), this.reject.bind(this))
    } catch (e) {
      this.reject(e);
    }
  }
  ```

**4. 实现 then 方法**

  4.1 实现 then 逻辑

    ```js
    // then接收两个参数, onFulfilled 和 onRejected
    then(onFulfilled, onRejected) {
      //检查并处理参数, 之前提到的如果不是function, 就忽略. 这个忽略指的是原样返回value或者reason.
      const fulFilledFn = this.isFunction(onFulfilled) ? onFulfilled : (value) => {
        return value;
      }
      const rejectedFn = this.isFunction(onRejected) ? onRejected : (reason) => {
        throw reason
      };
      // 根据当前 promise 的状态, 调用不同的函数
      switch (this.status) {
        case FULFILLED: {
          fulFilledFn(this.value);
          break;
        }
        case REJECTED: {
          rejectedFn(this.reason);
          break;
        }
        // then 可以链式调用，存储回调
        case PENDING: {
          this.FULFILLED_CALLBACK_LIST.push(realOnFulfilled);
          this.REJECTED_CALLBACK_LIST.push(realOnRejected);
          break;
        }
      }
    }
    ```

  4.2 status 发生变化时候去执行所有的回调

    ```js
    get status() {
      return this._status;
    }

    set status(newStatus) {
      this._status = newStatus;
      switch (newStatus) {
        case FULFILLED: {
          this.FULFILLED_CALLBACK_LIST.forEach(callback => {
            callback(this.value);
          })
          break;
        }
        case REJECTED: {
          this.REJECTED_CALLBACK_LIST.forEach(callback => {
            callback(this.reason);
          })
          break;
        }
      }
    }
    ```

**5. 实现 resolvePromise 方法**

**6. Promise.reject和Promise.resolve及catch实现**

**7. promise.race和promise.all实现**

#### 2. 贴下完整代码

```js
const PENDING = 'pending';
const FULFILLED = 'fulfilled';
const REJECTED = 'rejected';

class MPromise {

  FULFILLED_CALLBACK_LIST = [];
  REJECTED_CALLBACK_LIST = [];
  _status = PENDING;

  constructor(fn) {
    this.status = PENDING;
    this.value = null;
    this.reason = null;

    try {
      // bind: 改变 this 指向，返回一个函数
      fn(this.resolve.bind(this), this.reject.bind(this))
    } catch (e) {
      this.reject(e);
    }

  }

  get status() {
    return this._status;
  }

  set status(newStatus) {
    this._status = newStatus;
    switch (newStatus) {
      case FULFILLED: {
        this.FULFILLED_CALLBACK_LIST.forEach(callback => {
          callback(this.value);
        })
        break;
      }
      case REJECTED: {
        this.REJECTED_CALLBACK_LIST.forEach(callback => {
          callback(this.reason);
        })
        break;
      }
    }

  }
  
  resolve(value) {
    if (this.status === PENDING) {
      // 顺序注意一下: fulFilledFnWithCatch 用到
      this.value = value;
      this.status = FULFILLED;
    }
  }

  reject(reason) {
    if (this.status === PENDING) {
      this.reason = reason;
      this.status = REJECTED;
    }
  }

  then(onFulfilled, onRejected) {
    const fulFillFn = this.isFunction(onFulfilled) ? onFulfilled : (value) => {
      return value;
    };
    const rejectFn = this.isFunction(onRejected) ? onRejected : (reason) => {
      throw reason;
    };

    // 后续优化：用装饰器能少写一个try catch；isFunction精简一个
    const fulFilledFnWithCatch = (resolve, reject, newPromise) => {
      queueMicrotask(() => {
        try {
          if (!this.isFunction(onFulfilled)) {
            resolve(this.value);
          } else {
            const x = fulFillFn(this.value);
            this.resolvePromise(newPromise, x, resolve, reject);
          }
        } catch (e){
          reject(e);
        }
      })
    }

    const rejectedFnWithCatch = (resolve, reject, newPromise) => {
      queueMicrotask(() => {
        try {
          if (!this.isFunction(onRejected)) {
            reject(this.reason)
          } else {
            const x = rejectFn(this.reason);
            this.resolvePromise(newPromise, x, resolve, reject);
          }
        } catch {
          reject(e);
        }
      })
    }

    switch (this.status) {
      case FULFILLED: {
        // 这里可以直接调用Promise?
        const newPromise = new MPromise((resolve, reject) => {
          fulFilledFnWithCatch(resolve, reject, newPromise);
        })
        return newPromise;
      }
      case REJECTED: {
        const newPromise = new MPromise((resolve, reject) => {
          rejectedFnWithCatch(resolve, reject, newPromise);
        })
        return newPromise;
      }
      case PENDING: {
        const newPromise =  new MPromise((resolve, reject) => {
          this.FULFILLED_CALLBACK_LIST.push(() =>
            fulFilledFnWithCatch(resolve, reject, newPromise)
          );
          this.REJECTED_CALLBACK_LIST.push(() =>
            rejectedFnWithCatch(resolve, reject, newPromise)
          );
        })
        return newPromise;
      }
    }
  }

  resolvePromise(newPromise, x, resolve, reject) {
    // 一层一层找，直到 x 不是 function/object/promise，resolve 出去
    // 中间碰到报错都 reject 出去
    if (newPromise === x) {
      // TypeError 作用域找到了，做不合理的操作
      // ReferenceError 作用域都找不到该变量
      return reject(new TypeError('The promise and the return value are the same'))
    }
    if (x instanceof MPromise) {
      x.then((y) => {
        this.resolvePromise(newPromise, y, resolve, reject);
      }, reject)
    } else if (typeof x === 'object' || this.isFunction(x)) {
      if (x === null) {
        return resolve(x);
      }

      let then = null;

      try {
        then = x.then;
      } catch (error) {
        return reject(error);
      }

      if (this.isFunction(x)) {
        let called = false;
        try {
          // then 的 this 本来指向就为 x
          then.call(
            x,
            (y) => {
              if (called) return;
              called = true;
              this.resolvePromise(newPromise, y, resolve, reject);
            },
            (r) => {
              if (called) return;
              called = true;
              reject(r);
            }
          );
        } catch (error) {
          if (called) return;
          reject(error);
        }
      } else {
        resolve(x);
      }
    } else {
      resolve(x);
    }

  }

  catch(onRejected) {
    return this.then(null, onRejected)
  }

  isFunction(param) {
    return typeof param === 'function';
  }

  // add：默认产生一个成功的 promise。
  static resolve(value) {
    if (value instanceof MPromise) {
      return value;
    }

    return new MPromise((resolve) => {
      resolve(value);
    });
  }

  static reject(reason) {
      return new MPromise((resolve, reject) => {
          reject(reason);
      });
  }

  // static race(promiseList) {
  //   return new MPromise((resolve, reject) => {
  //       const length = promiseList.length;

  //       if (length === 0) {
  //           return resolve();
  //       } else {
  //           for (let i = 0; i < length; i++) {
  //               MPromise.resolve(promiseList[i]).then(
  //                   (value) => {
  //                       return resolve(value);
  //                   },
  //                   (reason) => {
  //                       return reject(reason);
  //                   });
  //           }
  //       }
  //   });
  // }

}
  ```

## 补充知识点

### 一、 迭代器 Iterator

ES6  引入的一种新的遍历机制，也是一种特殊对象，它具有一些专门为迭代过程设计的专有接口。

可调用 next() 方法，返回一个当前结果对象。

**next()方法：**

value：当前属性的值
done：用于判断是否遍历结束，当没有更多可返回的数据时，返回 true

### 二、 生成器 Generator

生成器是一种返回迭代器的函数，通过 function 关键字后的星号(\*)来表示，函数中会用到新的关键字 yield。星号可以紧挨着 function 关键字，也可以在中间添加一个空格.

### 三、 Async 和 Await

实际：Promise 语法糖，同步的形式写

![xmind总结](/static/xmind/frontEnd/relearn/promise.png)
