title: Vue2.0进阶 - 05. vue的style
author: OdinSam
tags:
  - style
  - vue2
categories:
  - 前端
  - vue2
abbrlink: '4576'
date: 2022-10-18 15:38:00
---
> [Vue2.0进阶学习](/articles/e255.html) 

<!--more-->

#### vue的style
1. 多个组件的style可能会出现class重名的情况，当class重名，后引入的组件样式会覆盖之前的同名样式
2. 可以在组件的 style 标签中添加 scoped属性，让当前style仅作用于当前组件(app的style不加scoped)
3. style 标签还有lang属性，默认是css。若要修改less，需注意:需要添加 less-loader.
4. npm view webpack version 可以查看对应版本
5. vue2 webpack使用的4.46 less-load 8以后得版本是为了迎合webpack5 所以需要安装less-loader 7版本
6. 安装 npm i less-loader@7

```js student.vue
<template>
    <div class="studv">
        <h2>Student组件</h2>
        <span class="namecls">姓名：{{stuName}}</span><br/>
        <span class="lessname">less 样式</span>
    </div>
</template>

<script>
export default {
    data() {
        return {
            stuName:'odinsam'
        }
    },
}
</script>

<style scoped lang="less">
.studv{
    background-color:bisque;
    width:400px;
    padding:50px;
    margin-left:50px;
    .lessname
    {
        font-size:30px;
    }
}
.namecls
    {
        color:blue;
    }
</style>
```

```js school.vue
<template>
    <div class="schooldv">
        <h2>School组件</h2>
        <span class="namecls">姓名：{{schoolName}}</span>
    </div>
</template>

<script>
export default {
    data() {
        return {
            schoolName:'vue学校'
        }
    },
}
</script>

<style scoped lang="css">
.schooldv{
    background-color:cadetblue;
    width:200px;
    padding:50px;
    margin-left:50px;
}
.namecls
{
    color:red;
}
</style>
```

[Vue2.0 基础学习目录](/articles/da3d.html)  

完整代码可以在 [GitHub](https://github.com/odinsam/learn-vue2.0)