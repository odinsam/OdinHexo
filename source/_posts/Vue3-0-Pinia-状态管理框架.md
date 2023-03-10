title: Vue3.0 - Pinia 状态管理框架
author: OdinSam
tags:
  - vue3
  - Pinia
categories:
  - 前端
  - vue3
abbrlink: 99b6
date: 2022-10-25 16:38:00
---
>Pinia 是 Vue 的存储库，它允许您跨组件/页面共享状态。与 Vuex 相比，Pinia 提供了一个更简单的 API，具有更少的规范，提供了 Composition-API 风格的 API，最重要的是，在与 TypeScript 一起使用时具有可靠的类型推断支持。

<!--more-->

#### 1. 与vuex的对比
1. pinia中只有state、getter、action，抛弃了Vuex中的Mutation，减少工作量。
2. pinia中的action支持同步和异步，Vuex不支持
3. 良好的Typescript支持，Vue使用TS来编写时使用pinia非常合适
4. 无需创建各个模块嵌套。在Vuex中如果数据过多，一般会通常分模块来进行管理，回略显得麻烦，而pinia中每个store都是独立的，互相不影响。
5. 体积非常小，只有1KB左右。
6. pinia支持插件来扩展自身功能。
7. 支持服务端渲染。

#### 2. 安装和使用
1. 安装：vue create vue-app 采用 Manually select features 形式安装。
```cmd
//上下移动  space空格选中  a全选  i反选    enter回车确定
Vue CLI v5.0.8
? Please pick a preset: Manually select features
? Check the features needed for your project: (Press <space> to select, <a> to toggle all, <i> to invert selection, and <enter> to proceed)
 ◉ Babel
 ◉ TypeScript
❯◯ Progressive Web App (PWA) Support
 ◉ Router
 ◯ Vuex
 ◉ CSS Pre-processors
 ◉ Linter / Formatter
 ◯ Unit Testing
 ◯ E2E Testing
```

在这里一般选择 babel/ts/router/css/linter这几个选项,选择好后 enter回车确认

```cmd
Vue CLI v5.0.8
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, TS, Router, CSS Pre-processors, Linter
? Choose a version of Vue.js that you want to start the project with (Use arrow keys)
❯ 3.x 
  2.x 
```

选择vue的版本，当然是选择vue3了。
```cmd
? Use class-style component syntax? (y/N) 
```

是否使用Class风格装饰器？ 原本是：home = new Vue()创建vue实例，用装饰器后：class home extends Vue{}。直接enter回车即选择No
```cmd
? Use Babel alongside TypeScript (required for modern mode, auto-detected polyfills, transpiling JSX)? (Y/n) 
```

使用Babel与TypeScript一起用于自动检测的填充? 选择yes
```cmd
Use history mode for router? (Requires proper server setup for index fallback in production)
```

是否使用router的history模式。router路由有hash模式和history模式（url带#和不带）.选择yes

```cmd
? Pick a CSS pre-processor (PostCSS, Autoprefixer and CSS Modules are supported by default): (Use arrow keys)
❯ Sass/SCSS (with dart-sass) 
  Less 
  Stylus
```

css处理模式。看情况选择。
```cmd
? Pick a linter / formatter config: (Use arrow keys)
❯ ESLint with error prevention only 
  ESLint + Airbnb config 
  ESLint + Standard config 
  ESLint + Prettier
```

代码检查与代码格式. 选择 ESLint + Prettier
```cmd
Pick additional lint features: (Press <space> to select, <a> to toggle all, <i> to invert selection, and <enter> to proceed)
❯◉ Lint on save
 ◯ Lint and fix on commit
```

项目校验格式 Lint on save 是保存时，Lint and fix on commit是提交时.选择 Lint on save
```cmd
? Where do you prefer placing config for Babel, ESLint, etc.? (Use arrow keys)
❯ In dedicated config files 
  In package.json 
```

项目的配置文件存放 In dedicated config files 独立配置文件，In package.json 存放在package.json。选择 In package.json
```cmd
? Save this as a preset for future projects? (y/N) 
```

询问保存该配置是否作为后续项目的可选配置，选择是，则会要求给该配置命名，这个自己定就行,我输入为：default-setting.配置后会在创建项目时：vue create projectName 看到这个配置。例如:
```cmd
Vue CLI v5.0.8
? Please pick a preset: 
❯ odinsam_vue+babel+ts+history+dart-sass+linter+prettier ([Vue 3] dart-sass, babel, typescript, router, eslint) 
  Default ([Vue 3] babel, eslint) 
  Default ([Vue 2] babel, eslint) 
  Manually select features 
```

2. 配置linter和prettier.添加 .prettierrc.js
```js .prettierrc.js
module.exports = {
  // 一行最多 180 字符
  printWidth: 180,
  // 使用 4 个空格缩进
  tabWidth: 2,
  // 不使用缩进符，而使用空格
  useTabs: false,
  // 行尾需要有分号
  semi: false,
  // 使用单引号
  singleQuote: true,
  // 对象的 key 仅在必要时用引号
  quoteProps: 'as-needed',
  // jsx 不使用单引号，而使用双引号
  jsxSingleQuote: false,
  // 末尾不需要逗号
  trailingComma: 'all',
  // 大括号内的首尾需要空格
  bracketSpacing: true,
  // jsx 标签的反尖括号需要换行
  jsxBracketSameLine: false,
  // 箭头函数，只有一个参数的时候，也需要括号  avoid 能省略括号的时候就省略 例如x => x
  arrowParens: 'avoid',
  // 每个文件格式化的范围是文件的全部内容
  rangeStart: 0,
  rangeEnd: Infinity,
  // 不需要写文件开头的 @prettier
  requirePragma: false,
  // 不需要自动在文件开头插入 @prettier
  insertPragma: false,
  // 使用默认的折行标准
  proseWrap: 'preserve',
  // 根据显示样式决定 html 要不要折行
  htmlWhitespaceSensitivity: 'css',
  // 换行符使用 lf
  endOfLine: 'auto',
}
```

3. 安装并使用pinia. npm i pinia 或者 yarn add pinia 。 main.js使用pinia
```js main.js
import { createApp } from "vue";
import App from "./App.vue";
import router from "./router";

createApp(App).use(router).mount("#app");
```

#### 3. 创建store.store的本质是一个函数包括两个参数，第一个是store的名字，第二个是store的配置项。配置项中包括 state,actions和getters. state 是 store 的核心部分是需要统一管理的状态.Actions 相当于组件中的 methods即业务操作.getter 完全等同于 Store 状态的 计算值

```js store/xxx.ts
import { defineStore } from 'pinia'

//第一个参数是store的名字，第二是参数是配置对象
export const useAccountStore = defineStore('account', {
  // 配置state
  state: () => {
    return { loginUser: 'odinsam', isLogin: false }
  },
  //配置getters
  getters: {
    getAddAge: state => {
      return !state.isLogin
    },
  },
  // 配置actions
  actions: {
    setUserName() {
      this.loginUser = 'action user'
      return { loginUser: this.loginUser }
    },
  },
})
```

#### 4. 使用store
```js HomView.vue
<template>
  <div class="home">
    <button @click="btnLogin">home login</button>
    <!--在模板中可以使用解构的store数据-->
    <span>store:{{ isLogin }}</span>
    <button @click="changeUserbtnLogin">change user</button>
    <span>user:{{ loginUser }}</span>
    <button @click="invokeAction">invoke action</button>
    <span>getter store:{{ accountStore.changeLoginState }}</span>
    <img alt="Vue logo" src="../assets/logo.png" />
    <HelloWorld msg="Welcome to Your Vue.js + TypeScript App" />
  </div>
</template>
<script setup lang="ts">
// 引入 store
import { useAccountStore } from '@/store/account'
// 引入 storeToRefs  将解构的store中的数据变为响应式数据
import { storeToRefs } from 'pinia'
import HelloWorld from '@/components/HelloWorld.vue' // @ is an alias to /src
// 调用函数创建store实例
const accountStore = useAccountStore()
function btnLogin() {
  console.log('btn login')
  // 在组件的methods中可以直接使用accountStore实例对象
  accountStore.isLogin = true
}
// 解构store的数据在模板中使用，并使其为响应式的数据
const { isLogin, loginUser } = storeToRefs(accountStore)
const changeUserbtnLogin = () => {
  console.log('function changeUserbtnLogin')
  accountStore.loginUser = 'abc'
}
const invokeAction = () => {
  console.log('function invokeAction')
  accountStore.setUserName()
}
/**
 可以使用 store.$onAction() 订阅 action 及其结果。 
 传递给它的回调在 action 之前执行。 
 after 处理 Promise 并允许您在 action 完成后执行函数。 
 以类似的方式，onError 允许您在处理中抛出错误。 这些对于在运行时跟踪错误很有用，类似于 Vue 文档中的这个提示。
 {
    name, // action 的名字
    store, // store 实例
    args, // 调用这个 action 的参数
    after, // 在这个 action 执行完毕之后，执行这个函数
    onError, // 在这个 action 抛出异常的时候，执行这个函数
  }
 */
accountStore.$onAction(({ name, store, args, after, onError }) => {
  //actions被调用触发
  console.log('anonymous function')
  //如果 actions 是 setUserName
  if (name === 'setUserName') {
    // 执行成功后出发
    after(reject => {
      console.log('store', store)
      console.log('args', args)
      //这里可以执行一些操作
      console.log('reject.loginUser:', reject.loginUser)
    })
  }
  // actions error时出发
  onError(error => {
    console.log('function onError', error)
  })
})
</script>
```

#### 5. pinia插件的使用plugins.
由于是底层 API，Pinia Store可以完全扩展。 您可以执行的操作列表：向 Store 添加新属性、定义 Store 时添加新选项、为 Store 添加新方法、包装现有方法、更改甚至取消操作、实现本地存储等副作用、仅适用于特定 Store。

#### 6. 使用 pinia.use() 将插件添加到 pinia 实例。给pania添加公共属性和公共方法。在vue中可以灵活使用
```js main.js
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'
import { createPinia } from 'pinia'
const pinia = createPinia()
// 扩展 PiniaCustomProperties 接口定义插件中添加的公共属性和公共方法。 然后可以安全地写入和读取它
declare module 'pinia' {
  export interface PiniaCustomProperties {
    pluginsAuthor: string
    pubs(): void
  }
}
//通过插件给pinia添加公共属性
pinia.use(context => {
  const { store } = context
  return { pluginsAuthor: store.$state.loginUser }
})
//通过插件给pinia添加公共属性
pinia.use(context => {
  const { store } = context
  store.pubs = function () {
    console.log('pinia plugins add public method')
  }
})
//因为context中可以解构出store，所以上边的代码可以简写为
// pinia.use(({ store }) => {
//   return { pluginsAuthor: store.$state.loginUser }
// })
// pinia.use(({ store }) => {
//   store.pubs = function () {
//     console.log('pinia plugins add public method')
//   }
// })

createApp(App).use(router).use(pinia).mount('#app')
```

```js HelloView.vue
<template>
<button @click="getPulicProto">pinia plugins public proto and invoke public methos</button>
<span>pluginsAuthor:{{ propto }}</span>
</template>
<script setup lang="ts">
let propto = ref('原始值')
const getPulicProto = () => {
  console.log('function getPulicProto')
  accountStore.pubs()
  propto.value = accountStore.pluginsAuthor
}
</script>
```





























