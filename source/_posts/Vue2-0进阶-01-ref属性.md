title: Vue2.0进阶 - 01. ref属性
author: OdinSam
tags:
  - vue2
  - ref属性
categories:
  - 前端
  - vue2
abbrlink: dd21
date: 2022-10-18 15:22:00
---
> [Vue2.0进阶学习](/articles/e255.html) 

<!--more-->

#### ref属性

1. 被用来给元素或者子组件注册引用信息(id的替代)
2. 应用在html标签上获取到的是真实的dom元素，应用在组件标签上获取到的是组件对象实例vc
3. 使用方法 &lt; h1 ref=“title”&gt;… &lt;/h1&gt; 或者 &lt;Student ref=“stu”&gt;&lt;/Student&gt;
4. 获取 this.refs.title 真实dom对象 或者 this.refs.stu stu组件对象实例

```js
<template>
  <div>
    <!--
        ref属性
        1. 被用来给元素或者子组件注册引用信息(id的替代)
        2. 应用在html标签上获取到的是真实的dom元素，应用在组件标签上获取到的是组件对象实例vc
        3. 使用方法 <h1 ref="title">.....</h1>  或者 <Student ref="stu"></Student>
        4. 获取 this.$refs.title 真实dom对象 或者 this.$refs.stu stu组件对象实例
    -->
    <h2 ref="title">app组件</h2>
    <Student ref="stu"></Student>
    <button @click="getDomClick">使用ref获取dom元素</button>
  </div>
</template>

<script>
import Student from './components/Student.vue';
export default {
    name: 'App',
    components: {
        Student
    },
    methods: {
        getDomClick() {
            console.log('function getDomClick');
            console.log(this.$refs.title);  //获取到真实的dom元素
            console.log(this.$refs.stu);    // student组件对象 vc
            
        }
    },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

[Vue2.0 基础学习目录](/articles/da3d.html)  

完整代码可以在 [GitHub](https://github.com/odinsam/learn-vue2.0)