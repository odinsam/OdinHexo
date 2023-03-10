title: Vue2.0进阶 - 03. mixins混入配置项
author: OdinSam
tags:
  - vue2
  - maxins
  - 混入
categories:
  - 前端
  - vue2
abbrlink: '6426'
date: 2022-10-18 15:32:00
---
> [Vue2.0进阶学习](/articles/e255.html) 

<!--more-->

#### mixins混入配置项
1. 可以吧多个组件公用的配置提取成一个混入对象
2. 定义见 mixin.js
3. 使用分为局部混合和全局混合。
4. 局部混合：引入混合对象 import {mixin,mixinData} from ‘…/mixin’;在vueComponent配置项中 mixins:[mixin,mixinData]
5. 全局混合：在main.js中 引入 import {mixin,mixinData} from ‘…/mixin’; 使用 Vue.mixin({ mixin, mixinData });混合
6. 当混入配置与组件配置冲突时，如果是data、methods等，以组件自身数据、方法为主。但如果是有冲突的生命周期钩子，则都会运行。且现执行混合中的钩子函数后执行组件自身的钩子函数

```js main.js
import Vue from 'vue';
import App from './App.vue';
import { mixin, mixinData } from './mixin';
Vue.config.productionTip = false;
//全局混入
Vue.mixin({ mixin, mixinData });
new Vue({
    render: (h) => h(App)
}).$mount('#app');
```

```js mixins
export const mixin = {
    methods: {
        showName() {
            alert(this.name);
        }
    },
    mounted() {
        console.log(this.name + '组件挂载了');
    }
};

export const mixinData = {
    data() {
        return {
            value: 'mixin数据'
        };
    }
};
```

```js school.vue
<template>
    <div class="studv">
        <h2>{{name}}</h2>
        <h2>mixindata与组件data冲突时:{{value}}</h2>
        <span>学校名：{{schoolName}}</span><br/>
        <button @click="showName">showName</button>
    </div>
</template>

<script>
import {mixin,mixinData} from '../mixin';
export default {
    data() {
        return {
            value:'组件data',
            name:'School组件',
            schoolName:'vue学院'
        }
    },
    mixins:[mixin,mixinData]
}
</script>

<style lang="css">
.studv{
    background-color:bisque;
    width:500px;
    padding:50px;
    margin-left:50px;
}
</style>
```

```js student.vue
<template>
    <div class="studv">
        <h2>{{name}}</h2>
        <span>姓名：{{stuName}}</span><br/>
        <button @click="showName">showName</button>
    </div>
</template>

<script>
import {mixin} from '../mixin.js';
export default {
    data() {
        return {
            name:'Student组件',
            stuName:'odinsam'
        }
    },
    mixins: [mixin],
    mounted() {
        console.log('student自身的mounted');
        
    },
}
</script>

<style lang="css">
.studv{
    background-color:bisque;
    width:500px;
    padding:50px;
    margin-left:50px;
}
</style>
```


[Vue2.0 基础学习目录](/articles/da3d.html)  

完整代码可以在 [GitHub](https://github.com/odinsam/learn-vue2.0)