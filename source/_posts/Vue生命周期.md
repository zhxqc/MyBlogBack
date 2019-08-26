---
title: Vue生命周期
date: 2019-08-20 10:01:53
tags: Vue
---

vue生命周期总共分为8个阶段创建前/后，载入前/后，更新前/后，销毁前/后。

<!-- more -->

> 创建前/后： 在beforeCreated阶段，vue实例的挂载元素! el和数据对象data都为undefined，还未初始化。在created阶段，vue实例的数据对象data有了，el还没有。

> 载入前/后：在beforeMount阶段，vue实例的$el和data都初始化了，但还是挂载之前为虚拟的dom节点，data.message还未替换。在mounted阶段，vue实例挂载完成，data.message成功渲染。

> 更新前/后：当data变化时，会触发beforeUpdate和updated方法。

> 销毁前/后：在执行destroy方法后，对data的改变不会再触发周期函数，说明此时vue实例已经解除了事件监听以及和dom的绑定，但是dom结构依然存在。



1. 什么是vue的声明周期 Vue 实例从创建到销毁的过程，就是生命周期。也就是从开始创建、初始化数据、编译模板、挂载Dom→渲染、更新→渲染、卸载等一系列过程，我们称这是 Vue 的生命周期。
2. 组件第一次加载会触发哪些声明周期函数 第一次页面加载时会触发 beforeCreate, created, beforeMount, mounted 这几个钩子
3. DOM 渲染在 哪个周期中就已经完成 DOM 渲染在 mounted 中就已经完成了。
4. 声明周期各个函数的作用 生命周期钩子的一些使用方法： beforecreate : 可以在这加个loading事件，在加载实例时触发 created : 初始化完成时的事件写在这里，如在这结束loading事件，异步请求也适宜在这里调用 mounted : 挂载元素，获取到DOM节点 updated : 如果对数据统一处理，在这里写上相应函数 beforeDestroy : 可以做一个确认停止事件的确认框 nextTick : 更新数据后立即操作dom
5. 当keep组件激活时会调用哪个生命周期的函数 activated 该钩子在服务器端渲染期间不被调用。
6. 当keep-alive组件停用时调用。 deactivated 该钩子在服务端渲染期间不被调用。
7. keep的调用时机 （炫酷网络）

activated调用时机：

第一次进入缓存路由/组件，在mounted后面，beforeRouteEnter守卫传给 next 的回调函数之前调用：

```
beforeMount=> 如果你是从别的路由/组件进来(组件销毁destroyed/或离开缓存deactivated)=>
mounted=> activated 进入缓存组件 => 执行 beforeRouteEnter回调

因为组件被缓存了，再次进入缓存路由/组件时，不会触发这些钩子：

// beforeCreate created beforeMount mounted 都不会触发。
所以之后的调用时机是：

组件销毁destroyed/或离开缓存deactivated => activated 进入当前缓存组件 
=> 执行 beforeRouteEnter回调
// 组件缓存或销毁，嵌套组件的销毁和缓存也在这里触发
```

1. 组件缓存和不缓存调用的生命周期

（1）不缓存： 进入的时候可以用created和mounted钩子，离开的时候用beforeDestory和destroyed钩子,beforeDestory可以访问this，destroyed不可以访问this。

（2）缓存了组件： 缓存了组件之后，再次进入组件不会触发beforeCreate、created 、beforeMount、 mounted，如果你想每次进入组件都做一些事情的话，你可以放在activated进入缓存组件的钩子中。 同理：离开缓存组件的时候，beforeDestroy和destroyed并不会触发，可以使用deactivated离开缓存组件的钩子来代替

1. 完整的触发顺序

   ```
    触发钩子的完整顺序：
   
    将路由导航、keep-alive、和组件生命周期钩子结合起来的，触发顺序，假设是从a组件离开，第一次进入b组件：
   
    1. beforeRouteLeave:路由组件的组件离开路由前钩子，可取消路由离开。
   
    2. beforeEach: 路由全局前置守卫，可用于登录验证、全局路由loading等。
   
    3. beforeEnter: 路由独享守卫
   
    4. beforeRouteEnter: 路由组件的组件进入路由前钩子。
   
    5. beforeResolve:路由全局解析守卫
   
    6. afterEach:路由全局后置钩子
   
    7. beforeCreate:组件生命周期，不能访问this。
   
    8. created:组件生命周期，可以访问this，不能访问dom。
   
    9. beforeMount:组件生命周期
   
    10. deactivated: 离开缓存组件a，或者触发a的beforeDestroy和destroyed组件销毁钩子。
   
    11. mounted:访问/操作dom。
   
    12. activated:进入缓存组件，进入a的嵌套子组件(如果有的话)。
   
    13. 执行beforeRouteEnter回调函数next。
   ```