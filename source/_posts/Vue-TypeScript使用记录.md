---
title: Vue+TypeScript使用记录
date: 2020-12-17 12:43:34
tags: vue TypeScript
---

最近参与开发了一个新项目，想着新项目就用用自己不熟悉的技术，一边学习一边进步，于是把目光瞄向了TypeScript，话不多说，直接开干。

<!--more-->

## 一、创建项目

项目搭建选择的是VueCli搭建的，写的时候试了一下发现Element-ui对vue3还没做支持，于是用的vue2+vue-property-decorator扩展（cli创建项目的时候自动引入了）配合TypeScript写的，css样式用scss写的。



## 二、项目结构

### 1、挂载全局方法

封装Vue的原因是我想把api接口挂载到原型链上

```
Vue.prototype.$XXX = XXX;
```

这样就不用在每个文件里手动引一次了，但是TS会报下面的错：

```
Property '$XXX' does not exist on type 'XXX'.
```

这时候不管这个错误能正常发送请求，能拿到请求的数据，但是打包的时候就GG了，build不成功，因为有代码逻辑有error (￣▽￣)" ，上网查Vue官方的解决方案如下：

```
增强类型以配合插件使用

插件可以增加 Vue 的全局/实例 property 和组件选项。在这些情况下，在 TypeScript 中制作插件需要类型声明。庆幸的是，TypeScript 有一个特性来补充现有的类型，叫做模块补充 (module augmentation)。

例如，声明一个 string 类型的实例 property $myProperty：

// 1. 确保在声明补充的类型之前导入 'vue'
import Vue from 'vue'

// 2. 定制一个文件，设置你想要补充的类型
//    在 types/vue.d.ts 里 Vue 有构造函数类型
declare module 'vue/types/vue' {
// 3. 声明为 Vue 补充的东西
  interface Vue {
    $myProperty: string
  }
}
在你的项目中包含了上述作为声明文件的代码之后 (像 my-property.d.ts)，你就可以在 Vue 实例上使用 $myProperty 了。

var vm = new Vue()
console.log(vm.$myProperty) // 将会顺利编译通过
你也可以声明额外的 property 和组件选项：

import Vue from 'vue'

declare module 'vue/types/vue' {
  // 可以使用 `VueConstructor` 接口
  // 来声明全局 property
  interface VueConstructor {
    $myGlobal: string
  }
}

// ComponentOptions 声明于 types/options.d.ts 之中
declare module 'vue/types/options' {
  interface ComponentOptions<V extends Vue> {
    myOption?: string
  }
}
上述的声明允许下面的代码顺利编译通过：

// 全局 property
console.log(Vue.$myGlobal)

// 额外的组件选项
var vm = new Vue({
  myOption: 'Hello'
})
```

这种解决方案应该是最合理的解决方案，在vue-router的组件的vue.d.ts文件中也见到如下：

```
/**
 * Augment the typings of Vue.js
 */

import Vue from 'vue'
import VueRouter, { Route, RawLocation, NavigationGuard } from './index'

declare module 'vue/types/vue' {
  interface Vue {
    $router: VueRouter
    $route: Route
  }
}

declare module 'vue/types/options' {
  interface ComponentOptions<V extends Vue> {
    router?: VueRouter
    beforeRouteEnter?: NavigationGuard<V>
    beforeRouteLeave?: NavigationGuard<V>
    beforeRouteUpdate?: NavigationGuard<V>
  }
}
```

当时写的时候没有找到这种方案，所以采用了封装vue的方式解决，代码如下

```
import { Vue } from 'vue-property-decorator'
import services from '@/services'

export default class VueBase extends Vue {
  public services = services

  private lockStack: Array<number> = [] // 锁栈
  private loading: any
  loadingStart() {
    if (!this.lockStack.length) {
      this.loading = this.$loading({
        background: 'rgba(0,0,0,0)',
      })
    }
    this.lockStack.push(1)
  }
  loadingDone() {
    if (this.lockStack.length <= 1) {
      this.loading.close()
    }
    this.lockStack.pop()
  }
}
```

然后每次新建vue文件的时候就不从`vue-property-decorator`中引vue了，而是extends自己封装的VueBase类。

### 2、Vuex使用

vuex也是由于打包问题，必须严格按照TS语法写，上网查资料看到好多vuex封装的组件，例如`vuex-class`，但是在写的时候看vuex的源码发现vuex已经对ts做了兼容。再加上新项目的业务线比较简单，于是没用其他组件，最终实现如下：

```
// user.ts
import { Commit } from 'vuex'
import createUserServices from '@/services/user'
import { Message } from 'element-ui'

const userServices = createUserServices()

const state: any = {
  userInfo: null,
}

const mutations: any = {
  UPDATE_USER_INFO(state: any, userInfo: any) {
    state.userInfo = userInfo
  },
}

const actions: any = {
  async getUserInfo(context: { commit: Commit }) {
    const { status, msg, data } = await userServices.getUserInfo()
    if (status !== 201) {
      Message.error(msg)
    }
    context.commit('UPDATE_USER_INFO', data)
  },
  async logOut(context: { commit: Commit }) {
    const { status, msg } = await userServices.logout()
    if (status !== 201) {
      Message.error(msg)
    }
    context.commit('UPDATE_USER_INFO', null)
  },
  clearInfo(context: { commit: Commit }) {
    context.commit('UPDATE_USER_INFO', null)
  },
}

export default {
  namespaced: true,
  state,
  mutations,
  actions,
}
```

```
// index.ts
import Vue from 'vue'
import Vuex from 'vuex'
import user from './modules/user'

Vue.use(Vuex)

export default new Vuex.Store({
  modules: {
    user,
  },
  getters: {
    userInfo: (state: any) => state.user.userInfo,
  },
})

// main.ts
new Vue({
  router,
  store,
  i18n,
  render: (h) => h(App),
}).$mount('#app')

```

使用的时候可以在文件中引入store使用，在vue文件中可以直接调用this.$store使用

### 3、EsLint配置

package.json

```
"scripts": {
    "lint": "eslint --ext .js,.vue src --fix"
  },
"devDependencies": {
    "@typescript-eslint/eslint-plugin": "^4.9.0",
    "@typescript-eslint/parser": "^4.9.0",
    "babel-eslint": "10.1.0",
    "eslint": "7.13.0",
    "eslint-config-prettier": "^6.15.0",
    "eslint-friendly-formatter": "4.0.1",
    "eslint-plugin-import": "2.22.1",
    "eslint-plugin-node": "11.1.0",
    "eslint-plugin-prettier": "^3.2.0",
    "eslint-plugin-promise": "4.2.1",
    "eslint-plugin-vue": "7.1.0",
    "prettier": "^2.2.1",
  }
```

.eslintrc.js

```
module.exports = {
  root: true,
  env: {
    es6: true,
    node: true
  },
  parserOptions: {
    ecmaVersion: 2020,
    parser: "@typescript-eslint/parser"
  },
  extends: [
    'plugin:@typescript-eslint/recommended',
    'prettier/@typescript-eslint',
    'plugin:vue/recommended',
    'plugin:prettier/recommended',
    "prettier/babel",
    "prettier/standard",
    "prettier/vue"
  ],
  rules: {
    'no-console': process.env.NODE_ENV === 'production' ? 'warn' : 'off',
    'no-debugger': process.env.NODE_ENV === 'production' ? 'warn' : 'off',
    "@typescript-eslint/explicit-module-boundary-types": 0,
    "@typescript-eslint/no-explicit-any": 0,
  }
}
```

.pretterrc.js

```
module.exports = {
  // tab缩进大小,默认为2
  tabWidth: 2,
// 使用tab缩进，默认false
// useTabs: true,
// 使用分号, 默认true
  semi: false,
// 使用单引号, 默认false(在jsx中配置无效, 默认都是双引号)
  singleQuote: true,
// 行尾逗号,默认none,可选 none|es5|all
// es5 包括es5中的数组、对象
// all 包括函数对象等所有可选
  TrailingCooma: "none",
// 对象中的空格 默认true
// true: { foo: bar }
// false: {foo: bar}
  bracketSpacing: true,
// JSX标签闭合位置 默认false
// false: <div
//          className=""
//          style={{}}
//       >
// true: <div
//          className=""
//          style={{}} >
  jsxBracketSameLine: false,
// 箭头函数参数括号 默认avoid 可选 avoid| always
// avoid 能省略括号的时候就省略 例如x => x
// always 总是有括号
  arrowParens: 'always',
  endOfLine: "auto" 
};
```

## 三、TS使用到的特性

interface、函数返回、声明文件等，其他跟es6差不多



## 四、总结

总体用下来感觉，技术只是技术，ts并没有比es6高档很多，更多的还是自己的选择。