title: Vue3.0 - 05. toRef与toRefs
author: OdinSam
tags:
  - vue3
  - toRef
  - toRefs
  - ''
categories:
  - 前端
  - vue3
abbrlink: ba93
date: 2022-10-21 16:29:00
---
> [Vue3.0 学习系列](/articles/151c.html) 

<!--more-->

#### toRef
1. 作用：创建一个对象，起value值指向另一个对象中的某个属性
2. const name = toRef(person,‘name’)
3. 应用：要将响应式对象中的某个属性单独提供给外部使用
4. toRefs 与 toRef 功能一致，但可以批量创建多个ref对象 toRefs(person)

```js student.vue
<template>
    <div>
        <h2>Student组件</h2>
        <div>
            <h3>{{person}}</h3>
            <h3>姓名:{{name}}</h3>
            <h3>年龄:{{age}}</h3>
            <h3>薪资:{{salary}}K</h3>
            <button @click="person.name+='~'">修改姓名</button>
            <button @click="person.age++">修改年龄</button>
            <button @click="person.job.j.salary++">修改薪资</button>
        </div>
    </div>
</template>
  
<script>
import { reactive, toRef, toRefs } from '@vue/reactivity'
/**

*/
export default {
    name: 'Student',
    setup(props, context) {
        let person = reactive({
            name: 'odinsam',
            age: 20,
            job: {
                j: {
                    salary:50
                }
            }
        })
        return {
            person,
            ...toRefs(person),
            salary:toRef(person.job.j,'salary')
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