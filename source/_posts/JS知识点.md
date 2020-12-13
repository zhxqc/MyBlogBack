---
title: JS知识点
date: 2020-06-10 16:11:58
tags: 面经
---

# 手写一个call

### call与apply

简单介绍：call和apply方法都是使用一个指定的this值和对应的参数前提下调用某个函数或方法。区别则在于call是通过传多个参数的方式，而apply则是传入一个数组。

<!--more-->

### 模拟实现

思路：在[JavaScript中的this指向](https://github.com/Abiel1024/blog/issues/15)说到了：函数还可以作为某个对象的方法调用，这时this就指这个上级对象。也就是我们平时说的，谁调用，this就指向谁。所以实现的方法就是在传入的对象中添加这么一个方法，然后再去执行这个方法。为了保持对象一直，在执行完之后再把这个对象给删除了。

```javascript
Function.prototype.myCall = (context, ...parameter) => {
    if (typeof context === 'object') {
        context = context || window
    } else {
        context = Object.create(null)
    }
    let fn = Symbol()
    context[fn] = this
    context[fn](...parameter)
    delete context[fn]
}

Function.prototype.myApply = (context, parameter) => {
    if (typeof context === 'object') {
        context = context || window
    } else {
        context = Object.create(null)
    }
    let fn = Symbol()
    context[fn] = this
    context[fn](...parameter)
    delete context[fn]
}
```



# 构造函数和new

## 对象的概念

​	“面向对象编程”（Object Oriented Programmming，缩写为OOP）是目前主流的编程范式。它的核心思想是把真实世界里的各种复杂关系，抽象成一个个的对象，然后由各个对象之间的分工和合作，对真实世界进行模拟。总结如下：

​	**对象是单个实物的抽象**

​	**对象是一个容器，封装了‘属性’和‘方法’**

​	一本书、一个人、一辆汽车都可以是一个对象，当实物抽象成了对象，实物之间的关系就变成对象之间的关系，从而模拟现实情况，针对对象进行编程。

​	所谓属性，就是对象的一种状态；所谓方法，就是对象的一种行为。

​	比如说，一个animal类，属性就是动物的身高体重大小颜色等状态，方法就是奔跑睡觉喝水等行为。

## 构造函数

​	面向对象编程的第一步，就是要生成对象。JS中面向对象编程时基于构造函数（constructor）和原型链（prototype）来实现的。

​	前面说过，对象时单个实物的抽象。实际编程中通常需要一个模板来表示某一事物的共同特征，然后对象根据这个模板生成。

​	JS语言中使用构造函数（constructor）来作为对象的模板（ES6语法中扩展了class语法糖）。所谓构造函数，就是提供一个生成对象的模板，并描述对象的基本结构的函数。

```javascript
var Keith = function(){
    this.height = 100;
}
// 同上
function Keith(){
    this.height = 100;
}
```

​	上面代码中，Krith就是一个构造函数，它提供模板，用来生成实例对象。为了与普通函数区别，构造函数的第一个字母通常大写。构造函数的特点：

1. ​	**构造函数的第一个字母通常大写**
2. ​    **函数体内使用this关键字，代表所要生成的实例对象**
3. ​    **生成对象时，必须用new命令来调用构造函数**

## new命令

### 基本原理

​	**new**命令的作用，就是执行一个构造函数，并返回一个实例对象，使用**new**命令时，它后面的函数调用就不是正常的调用，而是依次执行下面的步骤：

1. ​	**创建一个空对象，作为将要返回的对象实例**
2. ​    **将空对象的原型指向了构造函数的prototype属性**
3. ​    **将空对象赋值给构造函数内部的this关键字**
4. ​    **执行构造函数内部的代码**

   也就是说，构造函数内部，this指向的是一个新生成的空对象，所有针对this的操作，都会发生在这个空对象上。构造函数之所谓构造函数，意思是这个函数的目的就是操作一个空对象（即this）对象，将其构造为所需要的样子。

### 基本用法

​		当我们使用new来调用构造函数时，new命令会创建一个空对象boy，作为将要返回的实例对象。接着，这个空对象的原型会指向构造函数Parent的prototype属性。也就是boy.prototype===Person.prototype的。要注意的是空对象指向构造函数Parent的prototype属性，而不是指向构造函数本身。然后，我们将这个空对象赋值给构造函数内部的this关键字。也就是说，让构造函数内部的this关键字指向一个对象实例。最后，开始执行构造函数内部代码。

​		一个问题，如果我们忘记使用new命令来调用构造函数，直接调用构造函数了，会发生什么？

　　这种情况下，构造函数就变成了普通函数，并不会生成实例对象。而且由于后面会说到的原因，`this`这时代表全局对象

　	为了保证构造函数必须与`new`命令一起使用，一个解决办法是，在构造函数内部使用严格模式，即第一行加上`use strict`。

```
     function Person(name, height) {
         'use strict';
         this.name = name;
         this.height = height;
     }
     var boy = Person();
     console.log(boy) //TypeError: name is undefined
```

​		上面代码的`Person`为构造函数，`use strict`命令保证了该函数在严格模式下运行。由于在严格模式中，函数内部的`this`不能指向全局对象。如果指向了全局，this默认等于`undefined`，导致不加`new`调用会报错（JavaScript不允许对`undefined`添加属性）。

　　另一个解决办法，是在构造函数内部判断是否使用`new`命令，如果发现没有使用，则直接返回一个实例对象。

```
     function Person(name, height) {
     if (!(this instanceof Person)) {
            return new Person(name, height);
         }
        this.name = name;
         this.height = height;
     }
     var boy = Person('Keith');
     console.log(boy.name) //'Keith'
```

​		如果构造函数内部有`return`语句，而且`return`后面跟着一个复杂数据类型（对象，数组等），`new`命令会返回`return`语句指定的对象；如果return语句后面跟着一个简单数据类型（字符串，布尔值，数字等），则会忽略return语句，返回this对象。如果对普通函数（内部没有`this`关键字的函数）使用`new`命令，则会返回一个空对象。