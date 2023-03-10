title: Vue3.0 - 07. vue3中新增的一些组件
author: OdinSam
tags:
  - vue3
categories:
  - 前端
  - vue3
abbrlink: '40e7'
date: 2022-10-22 16:34:00
---
> [Vue3.0 学习系列](/articles/151c.html) 

<!--more-->

#### 1. Fragment
在vue2中，组件时必须有一个跟标签的
在vue3中，组件可以没有跟标签，内部会将多个标签包含在一个Fragment虚拟元素中
优势在于可以减少标签层级、减小内存占用
#### 2. Teleport
teplport是一种能够将我们的组件的html结构移动到指定位置的技术
移动的位置可以写html元素也可以是css选择： body或者#root 等
```js 
<teleport to="移动的位置">
	<div>
    	需要移动的组件
    </div>
</teleport>
```
#### 3. Suspense
等待异步组件时渲染一些额外内容，让应用有更好的用户体验
使用Suspense并使用异步动态引入对象，可以在setup中返回异步结果 return { await new Promise() }
```js 
<template>
	<Suspense>
    	<template v-slot:default>
        	<Child/>
        </template>
        <template v-slot:fallback>
        	<Loading/>
        </template>
    </Suspense>
</template>

<script>
// 异步动态引入组件
const child = defineAsyncComponent(()=>return import('./components/xxx.vue'))
</script>
```

#### 4. 全局API的转移

|vue2 全局api|	vue3 全局api|
|--|--|
|Vue.config.xxx	|app.config.xxx|
|Vue.config.productionTip|	移除|
|Vue.component	|app.component|
|Vue.directive	|app.directive|
|Vue.mixin	|app.mixin|
|Vue.use	|app.use|
|Vue.prototype	|app.config.globalProperties|

#### 5. 一些其他的改变
1. data在vue3中必须是函数形式
2. 过度类名的更改 vue3中 v-enter-from v-leave-to
3. 移除keycCode作为v-on的修饰符,同时也不支持config.keyCodes
4. 移除v-on.native修饰符
5. 移除过滤器 filter

[Vue2.0 基础学习目录](/articles/da3d.html)  

[Vue2.0进阶学习](/articles/e255.html) 

完整代码可以在 [GitHub](https://github.com/odinsam/learn-vue2.0)