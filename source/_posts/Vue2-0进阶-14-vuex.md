title: Vue2.0进阶 - 14. vuex
author: OdinSam
tags:
  - vue2
  - vuex
categories:
  - 前端
  - vue2
abbrlink: 97b5
date: 2022-10-20 16:01:00
---
> [Vue2.0进阶学习](/articles/e255.html) 

<!--more-->

#### vuex
vue组件通过dispatch('key',param)找到store的actions(key,param),action通过commit找到mutations(key,function),mutations执行回调函数修改state中的数据，vue根据state数据的改变自动重新render模板.
vue组件也可以直接通过commit找到mutations。这种情况一般用在所需要的参数是固定已知的情况下

1. 安装vuex vue2.0需要安装vuex3 npm i vuex@3 2. 创建 store/index.js。store中包含actions、mutations、state、getters。

```js store/index.js
import Vue from 'vue';
import Vuex from 'vuex';
Vue.use(Vuex);
// 模块化创建storeOptions
const stuOptions = {
    namespaced: true,
    actions: {
        add(context, param) {
            console.log('action add', param);
            context.commit('ADD', param);
        }
    },
    mutations: {
        ADD(state, param) {
            console.log('mutations add');
            const stuId =
                state.stus.length === undefined ? 1 : state.stus.length + 1;
            const stu = { id: stuId, stuName: stuId + '-' + param.stuName };
            state.stus.push(stu);
        }
    },
    state: { stus: [] },
    getters: {
        bigSum(state) {
            console.log(this);
            return state.stus.length * 10;
        }
    }
};
//创建store
export default new Vuex.Store({
    modules: {
        stuOptions
    }
});
```

2. 创建vue时，在配置项中配置store
```js main.js
new Vue({
    store,
    render: (h) => h(App)
}).$mount('#app');
```
3. 在组件中使用store

```js student.vue
<template>
    <div class="studv">
        <h2>Student组件</h2>
        <div>放大十倍后的学生人数:{{$store.getters['stuOptions/bigSum']}}</div>
        <ul>
            <li v-for="s in $store.state.stuOptions.stus" :key="s.id">{{s.stuName}}</li>
        </ul>
    </div>
</template>

<script>

export default {
    data() {
        return {
            
        }
    },
    mounted() {
        console.log(this.$store);
        
    },
}
</script>

<style lang="css">
.studv{
    background-color:bisque;
    width:200px;
    padding:50px;
    margin-left:50px;
}
</style>
```

```js app.vue
<template>
  <div class="dvapp">
    <h2>app组件</h2>
    <span>共计学生{{stusSum}},</span><button @click="addStu">添加学生</button><br/>
    <Student></Student><br/>
  </div>
</template>

<script>
import Student from './components/Student.vue';
import {mapState,mapGetters,mapMutations} from 'vuex';

export default {
    name: 'App',
    components: {
        Student
    },
    methods: {
        addStu() {
            this.$store.dispatch('stuOptions/add', { stuName: 'odin' });
            //跳过dispatch字节commit到mutations
            // this.$store.commit('ADD',{ stuName: 'odin' })
        },
        //使用映射的形式 映射mutations
        // ...mapMutations(['ADD'])
        // ...mapMutations('stuOptions',['ADD'])
    },
    computed: {
        stusSum() {
            console.log(this.$store);
            return this.$store.state.stuOptions.stus.length
        },
        //使用映射的形式 映射state为计算属性
        // ...mapState(['stus'])
        ...mapState('stuOptions',['stus'])
        //...mapGetters(['stus'])
    }
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
.dvapp
{
    background-color: aquamarine;
}
</style>
```

[Vue2.0 基础学习目录](/articles/da3d.html)  

完整代码可以在 [GitHub](https://github.com/odinsam/learn-vue2.0)