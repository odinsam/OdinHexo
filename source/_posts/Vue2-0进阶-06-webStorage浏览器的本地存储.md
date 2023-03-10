title: Vue2.0进阶 - 06. webStorage浏览器的本地存储
author: OdinSam
tags:
  - webstorage
  - vue2
categories:
  - 前端
  - vue2
abbrlink: fef3
date: 2022-10-19 15:40:00
---
> [Vue2.0进阶学习](/articles/e255.html) 

<!--more-->

1. 浏览器通过 window.sessionStorage 和 window.localStorage 属性实现本地存储
2. 相关api
xxxStorage.setItem(‘key’,‘value’) 存储数据
xxxStorage.getItem(‘key’) 读取数据
xxxStorage.removeItem(‘key’) 删除某个数据
xxxStorage.clear() 清空所有数据
3. sessionStorage 存储的内容会随着浏览器的关闭而消失.
4. localStorage 存储的内容需要手动调用api清除
5. xxxStorage.getItem(‘key’) 如果key不存在，则返回null
6. JSON.parse(null) 返回的依然是null

```js student.vue
<template>
    <div class="studv">
        <h2>Student组件</h2>
        <input type="text" v-model.lazy="addStuName" @keyup.enter="addStu"><br/>
        <ul>
            <li v-for="stu in stus" :key="stu.id">{{stu.stuName}}</li>
        </ul>
        <button @click="changeStuName">修改第一个学生的姓名</button>
    </div>
</template>

<script>
export default {
    data() {
        return {
            addStuName: '',
            //从localStorage中获取数据 如果为null则返回空数组。确保用户打开浏览器时显示的是上次保存在localStorage中的数据
            stus: JSON.parse(localStorage.getItem('stu')) || []
        }
    },
    methods: {
        addStu() {
            const newStu = { id: this.stus.length, stuName: this.addStuName };
            this.stus.push(newStu);
            this.addStuName=''
        },
        //修改第一个学生的姓名，此时需要开启深度监视，否则无法watch无法监视到stu内部的元素属性的修改，就无法触发监视事件（修改localStorage中的数据）
        changeStuName() {
            this.stus[0].stuName = this.stus[0].stuName+'change'
        }
    },
    watch: {
        stus: {
            //放弃watch的简写方式开启深度监视，确保当修改了stu数组内部元素的属性后依旧可以监视到数据改变并存储到localStorage中
            deep: true,
            handler(value) {
                localStorage.setItem('stu',JSON.stringify(value))
            }
        }
    }
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

[Vue2.0 基础学习目录](/articles/da3d.html)  

完整代码可以在 [GitHub](https://github.com/odinsam/learn-vue2.0)