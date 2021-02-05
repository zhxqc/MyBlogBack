---
title: 详解Promise
date: 2021-01-29 15:43:08
tags: JavaScript
image: https://raw.githubusercontent.com/zhanghengGetUp/pic/main/post-image1.jpg
---

### 两个重点

1. 一个Promise中的当前状态仅可能是pending、fufilled、rejected三者之一，切换状态仅能单向从pending向其他两种状态转化，不可逆
2. Promise中的then方法可接受两个参数作为Promise状态改变时的回调，且then会返回一个新的Promise，可以被同一个Promise调用多次

<!-- more -->

### 实现

#### 骨架

根据用户的使用方法，构建架构的骨架

```
class Promise{
    constructor(execute) {
        this.status = STATES.PENDING
        this.value = null
        this.reason = null
        this.fullfilledCbs = []
        this.rejectedCbs = []
        const resolve = () => { }
        const reject = () => { }
        execute(resolve,reject)
    }

    then(fullfilledCbs, rejectedCbs) { 
        return new Promise()
    }
}
```

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

  then(fullfilledCbs, rejectedCbs) {
    if (this.status === STATES.FULFILLED) {
      onFulfilled(this.value);
    } else if (this.status === STATES.REJECTED) {
      onRejected(this.reason);
    }
  }
}
```

