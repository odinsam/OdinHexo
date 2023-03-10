title: Vue3.0 - 04. vue3中的hook
author: OdinSam
tags:
  - vue3
  - hook
categories:
  - 前端
  - vue3
abbrlink: bd63
date: 2022-10-21 16:27:00
---
> [Vue3.0 学习系列](/articles/151c.html) 

<!--more-->

#### hook
1. hook的本质是一个函数，把setup函数中使用的组合api进行封装。
2. hook有点类似vue2中的mixin。
3. 自定义hook可以让setup中的逻辑更加清楚易懂

```js student.vue
<template>
    <div>
        <h2>{{title}}</h2>
        <div>点击的鼠标的坐标是:{{mpoint.x}},{{mpoint.y}}</div>
        <div>hook中回传的信息:{{param.title}}</div>
    </div>
</template>
  
<script>
/**

*/
import { ref } from 'vue';
//引入自定义hook函数
import usePoint from '../hooks/usePoint';
export default {
    name: 'Student',
    setup(props, context) {
        let title = ref('Student组件')
        let {mpoint,param } = usePoint({title:title.value});
        return {
            title,mpoint,param
        }
    }
}
</script>
  
<style>
</style>
```

```js hooks/usePoint.js
import { onBeforeUnmount, onMounted, reactive } from '@vue/runtime-core';
export default function (param) {
    let mpoint = reactive({ x: 0, y: 0 });
    function savePotin(event) {
        mpoint.x = event.pageX;
        mpoint.y = event.pageY;
        param.title = param.title + '-hook';
        console.log('param', param);
        console.log('mouse click point,', event.pageX, event.pageY);
    }
    onMounted(() => {
        window.addEventListener('click', savePotin);
    });
    onBeforeUnmount(() => {
        window.removeEventListener('click', savePotin);
    });
    //使用hook一定要给当前函数返回信息
    return { mpoint, param };
}

```

```js app.vue
<template>
    <div>
        <h2>app组件</h2>
        <button @click="isShowStuCpm">显示、隐藏student组件</button>
        <Student v-if="isShow"></Student>
    </div>
</template>

<script>
import {ref} from 'vue';
import Student from './components/Student.vue';
export default {
    name: 'App',
    components: {
        Student
    },
    setup(props, context) {
        let isShow = ref(true)
        let isShowStuCpm = function () {
            isShow.value = !isShow.value
        }
        return {
            isShow,isShowStuCpm
        }
    }
}
</script>

<style>
</style>
```


[Vue2.0 基础学习目录](/articles/da3d.html)  

[Vue2.0进阶学习](/articles/e255.html) 

完整代码可以在 [GitHub](https://github.com/odinsam/learn-vue2.0)