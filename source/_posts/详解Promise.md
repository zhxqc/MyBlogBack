---
title: 详解Promise
date: 2021-01-29 15:43:08
tags: JavaScript
image: https://raw.githubusercontent.com/zhanghengGetUp/pic/main/post-image1.jpg
---

本文介绍一下Promise

<!-- more -->

## 介绍

​		异步行为是JavaScript的基础，但以前实现不理想。在早起的JavaScript中，只支持定义回调函数来表明异步函数操作完成。串联多个异步操作是一个常见的问题，通常需要深度嵌套回调函数（俗称“回调地狱”）来解决。

​		为了解决地狱回调这个问题，ES6根据Promise/A+规范，实现了一个Promise，可以将异步行为以同步行为的流程表达出来。

## Promise/A+规范

​		一个Promise代表一个异步操作的最终结果，主要操作方法是通过调用promise的then方法，它接受的回调函数接受promise成功的结果或失败的原因。

### 1、术语

1. **promise**是一个有符合此标准的**then**方法的**object**或**function**
2. **thenable**是then方法定义的**object**或**function**
3. **value**是一个JavaScript合法值（包括**undefined**，**thenable**，**promise**）
4. **exception**是一个**throw**语句抛出的错误
5. **reason**是一个表明**promise**失败的原因的值

### 2、要求

1. #### Promise状态

   一个promise有且只有一个状态（**pending**、**fullfilled**、**rejected**其中之一）

   1. pending状态时：
      - 可能会转化为fullfilled状态或rejected状态
   2. fullfilled状态时：
      - 不能再转变状态为其他状态
      - 必须有一个value，且不可改变
   3. rejected状态时：
      - 不能再转变状态为其他状态
      - 必须有一个reason，且不可改变

   注：这里的**不可改变**意思是恒等（即可用===判断相等），但不意味更深层次的不可变（当 value 或 reason 不是基本值时，只要求其引用地址相等，但属性值可被更改）

2. #### then方法

   一个promise必须提供一个then方法，用来获取当前或最终的value或reason

   一个promise的then方法接受两个参数：

   `promise.then(onFullfilled, onRejected)`

   1. onFullfilled和onRejected都是可选参数：

      - 如果onFullfilled不是函数它会被忽略
      - 如果onRejected不是函数它会被忽略

   2. onFullfilled特性：

      - 它一定是在 promise 是 fulfilled 状态后调用，并且接受一个参数 value
      - 在 `promise` 执行结束前其不可被调用
      - 它最多被调用一次

   3. onRejected特性：

      - 它一定在 promise 是 rejected 状态后调用，并且接受一个参数 reason
      - 在 `promise` 被拒绝执行前其不可被调用
      - 它最多被调用一次

   4. onFulfilled 或 onRejected 只在执行环境堆栈只包含平台代码之后调用

   5. onFulfilled 和 onRejected 会作为函数形式调用 (也就是说，默认 `this` 指向 global，严格模式 `undefined`)

   6.  promise 的 `then` 可以链式调用多次

      - 当 promise 转态是 fulfilled 时，所有的 onFulfilled 回调会以他们注册时的顺序依次执行
      - 当 promise 转态是 rejected 时，所有的 onRejected 回调会以他们注册时的顺序依次执行

   7. `then` 方法必须返回一个 `promise` 对象

      `promise2 = promise1.then(onFulfilled, onRejected); `

      1. 如果 onFulfilled 或 onRejected 返回的是一个 x，那么它会以`[[Resolve]](promise2, x)` 处理解析
      2. 如果 onFulfilled 或 onRejected 里抛出了一个异常，那么 promise2 必须捕获这个错误（接受一个 reason 参数）
      3. 如果 onFulfilled 不是一个函数，并且 promise1 状态是 fulfilled，那么 promise2 一定会接受到与 promse1 一样的值 value
      4. 如果 onRejected 不是一个函数，并且 promise1 状态是 rejected，promise2 一定会接受到与 promise1 一样的值 reason

3. #### Promise处理程序

   ​	**Promise 解决过程**是一个抽象的操作，其需输入一个 `promise` 和一个值，我们表示为 `[[Resolve]](promise, x)`，如果 x 是 thenable 类型，它会尝试生成一个 promise 处理 x，否则它将直接 resolve x

   ​	这种 **thenable** 的特性使得 Promise 的实现更具有通用性：只要其暴露出一个遵循 Promise/A+ 协议的 `then` 方法即可；这同时也使遵循 Promise/A+ 规范的实现可以与那些不太规范但可用的实现能良好共存。

   （链式调用，层层传递下去）。它也允许对那些不符合 Promises/A+ 的 then 方法进行 “吸收”







1. 一个Promise中的当前状态仅可能是pending、fufilled、rejected三者之一，切换状态仅能单向从pending向其他两种状态转化，不可逆
2. Promise中的then方法可接受两个参数作为Promise状态改变时的回调，且then会返回一个新的Promise，可以被同一个Promise调用多次



## 实现

### 骨架

根据用户的使用方法，构建架构的骨架

```
class Promise{
    constructor(execute) {
        this.status = STATES.PENDING
        this.value = null
        this.reason = null
        // 存放成功的回调
    		this.fullfilledCbs = [];
    		// 存放失败的回调
    		this.rejectedCbs= [];
        const resolve = () => { }
        const reject = () => { }
        execute(resolve,reject)
    }

    then(fullfilledCbs, rejectedCbs) { 
        return new Promise()
    }
}
```

### 简单实现

先不考虑各种情况，只是简单去实现，不去考虑异步，返回值等条件的结构。

```
const STATES = {
  PENDING: "PENDING",
  FULFILLED: "FULFILLED",
  REJECTED: "REJECTED",
};

class Promise {
  constructor(execute) {
    this.status = STATES.PENDING;
    this.value = null;
    this.reason = null;
    this.fullfilledCbs = [];
    this.rejectedCbs= [];

    const resolve = (value) => {
      if (this.status === STATES.PENDING) {
        this.value = value;
        this.status = STATES.FULFILLED;
        this.fullfilledCbs.forEach((_i) => _i());
      }
    };
    const reject = (err) => {
      if (this.status === STATES.PENDING) {
        this.reason = err;
        this.status = STATES.REJECTED;
        this.rejectedCbs.forEach((_i) => _i());
      }
    };

    try {
      execute(resolve, reject);
    } catch (e) {
      reject(e);
    }
  }

  then(onFulfilled, onRejected) {
    if (this.status === STATES.FULFILLED) {
      onFulfilled(this.value);
    } else if (this.status === STATES.REJECTED) {
      onRejected(this.reason);
    }
    if (this.status === STATES.PENDING) {
      // 如果promise的状态是 pending，需要将 onFulfilled 和 onRejected 函数存放起来，等待状态确定后，再依次将对应的函数执行
      this.fullfilledCbs.push(() => {
        onFulfilled(this.value)
      });
      this.rejectedCbs.push(()=> {
        onRejected(this.reason);
      })
    }
  }
}
```

#### 测试一下

```
const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('成功');
  },1000);
}).then(
  (data) => {
    console.log('success', data)
  },
  (err) => {
    console.log('faild', err)
  }
)

等待1秒后控制台输出： success 成功
```

### then方法补全

