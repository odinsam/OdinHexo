title: Vue2.0进阶 - 04. plugins插件
author: OdinSam
tags:
  - vue2
  - plugins
categories:
  - 前端
  - vue2
abbrlink: ec79
date: 2022-10-18 15:36:00
---
> [Vue2.0进阶学习](/articles/e255.html) 

<!--more-->

#### plugins插件
1. 包含install方法的对象，install的第一个参数是vue，第二个以后得参数是插件使用者传递的数据
2. 插件可以给vue添加实例方法、实例属性
3. 使用插件： 在创建Vue实例前 Vue.use(plugins, { value1: ‘value1’, value2: ‘value2’ });
4. 插件也可以 添加全局过滤器、添加全局指令、添加全局混入

```js plugins.js
/*
    vue插件
    1. 包含install方法的对象，install的第一个参数是vue，第二个以后得参数是插件使用者传递的数据
    2. 插件可以给vue添加实例方法、实例属性
    3. 使用插件： 在创建Vue实例前 Vue.use(plugins, { value1: 'value1', value2: 'value2' });
    4. 插件也可以 添加全局过滤器、添加全局指令、添加全局混入
*/
export default {
    install(Vue, options) {
        console.log('install 插件');
        console.log('options', options);
        //添加实例方法、实例属性
        Vue.prototype.pluginMethod = function (value) {
            console.log('invoke plugins myMethod');
            console.log('myMethod param value', value);
        };
        Vue.prototype.pluginPrototype = 'odinsam plugins';
        //添加全局过滤器
        Vue.filter('odinFilter', function (value) {
            console.log('odinFilter 被调用');
            return value + '-odinFilter 被调用';
        });
        //添加全局指令
        Vue.directive('big', {
            bind(ele, binding) {
                console.log(
                    '1次调用 - 当指令与元素绑定成功时调用，在内存，页面并没有元素'
                );
                console.log('binding', binding);
                ele.innerText = 'v-big指令显示' + binding.value;
            },
            inserted(ele, binding) {
                console.log('1或n次调用 - 指令所在的元素被插入页面时调用');
            },
            update(ele, binding) {
                console.log('1或n次调用: 当指令所在模板被重新解析时');
            }
        });
        //添加全局混入
        Vue.mixin({
            data() {
                return {
                    pluginMixinValue: 'plugins mixin data'
                };
            }
        });
    }
};
```

```js main.js 使用插件
import Vue from 'vue';
import App from './App.vue';
import plugins from './plugins';
Vue.config.productionTip = false;
//使用插件
Vue.use(plugins, { value1: 'value1', value2: 'value2' });
new Vue({
    render: (h) => h(App)
}).$mount('#app');
```

```js student.vue
<template>
    <div class="studv">
        <h2>Student组件</h2>
        <span>姓名：{{stuName}}</span><br/>
        <span>插件混入+插件过滤器:{{pluginMixinValue | odinFilter}}</span><br/>
        <span>插件v-big指令:</span><span v-big="value"></span><br/>
        <button @click="invokeMyMethod">调用插件中的myMethod方法</button>
    </div>
</template>

<script>
export default {
    data() {
        return {
            stuName: 'odinsam',
            value:'组件value'
        }
    },
    methods: {
        invokeMyMethod() {
            this.pluginMethod(this.pluginPrototype);
        }
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