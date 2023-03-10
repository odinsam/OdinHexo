title: Vue2.0 - 18.4 Vue组件-单文件组件
author: OdinSam
tags:
  - component
  - vue2
  - ''
categories:
  - 前端
  - vue2
abbrlink: 9c18
date: 2022-10-18 15:05:00
---
> [vue2.0 基础学习目录](/articles/da3d.html) 

<!--more-->

```html html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>18.4.单文件组件</title>
        <style></style>
    </head>
    <body>
        <div id="root"></div>
    </body>
    <script src="../js/vue.js"></script>
    <script src="main.js"></script>
    <script>
        Vue.config.productionTip = false;
    </script>
</html>
```

```js main.js
import App from 'App';
const vm = new Vue({
    el: '#root',
    components: {
        App
    },
    template: `<App></App>`
});
```

```js app.vue
<template>
    <div class="app">
        <h2>App组件</h2>  
        <Student></Student>
    </div>
</template>

<script>
import Student from './Student.vue';
export default {
    name: 'App',
    components:{ Student }
}
</script>

<style scoped>
.studv{
    background-color:aquamarine;
    padding: 20px;   
}
```

```js student.vue
<template>
    <div class="studv">
        <h2>Studen组件</h2>    
        <span>{{stuName}}</span>
    </div>
</template>

<script>
export default {
    name: 'Student',
    data() {
        return {
            stuName:'odinsam'
        }
    }, 
}
</script>

<style scoped>
.studv{
    margin-left:50px;
    background-color: bisque;
    padding: 20px;   
}
</style>
```


[vue2.0 进阶学习的目录](/articles/e255.html)  

完整代码可以在 [GitHub](https://github.com/odinsam/learn-vue2.0)