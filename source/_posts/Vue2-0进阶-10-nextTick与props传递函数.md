title: Vue2.0进阶 - 10. nextTick与props传递函数
author: OdinSam
tags:
  - nexttick
  - props
  - vue2
categories:
  - 前端
  - vue2
abbrlink: fc6a
date: 2022-10-19 15:50:00
---
> [Vue2.0进阶学习](/articles/e255.html) 

<!--more-->

#### nextTick与props传递函数
1. this.$nextTick(回调函数) 下一次dom更新结束后执行回调函数
2. 使用时机： 当改变数据后，要基于更新后的新dom进行某些操作时，使用nextTick利用回调函数执行操作
3. 父组件可以通过props给子组件传递函数，当子组件执行函数回调时，回传数据达到子组件向父组件传递数据的目的

```js student.vue
<template>
    <!--
        nextTick与props传递函数
        1. this.$nextTick(回调函数) 下一次dom更新结束后执行回调函数
        2. 使用时机： 当改变数据后，要基于更新后的新dom进行某些操作时，使用nextTick利用回调函数执行操作
        3. 父组件可以通过props给子组件传递函数，当子组件执行函数回调时，回传数据达到子组件向父组件传递数据的目的
    -->
    <div class="studv">
        <h2>Student组件</h2>

        <span v-if="!isEdit">姓名：{{stuName}}</span>
        <span v-else><input ref="txtStuName" type="text" v-model="stuName"></span>
        <br/>
        <button @click="editOrOkClick">{{btnText[isEdit]}}</button>
    </div>
</template>

<script>
export default {
    data() {
        return {
            isEdit:false,
            stuName: 'odinsam',
            btnText:{true:'确定',false:'编辑'}
        }
    },
    //app组件传递的回调函数
    props:['getStuName'],
    methods: {
        editOrOkClick() {
            if (this.isEdit)
            {
                //子组件调用父组件通过props传递的回调函数向父组件传递数据
                this.getStuName({stuName:this.stuName});
            }
            this.isEdit = !this.isEdit;
            // 在本次操作执行完成dom修改后，执行回调函数，使新出现的文本框获得焦点
            this.$nextTick(function () {
                if (this.isEdit) {
                    this.$refs.txtStuName.focus();
                }
            })
            
        }
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
    <br/>
    <span>学生的姓名是:{{stuName}}</span>
    <Student :getStuName="getStuName"></Student>
  </div>
</template>

<script>
import Student from './components/Student.vue';
export default {
    name: 'App',
    data() {
        return {
            stuName:''
        }
    },
    components: {
        Student
    },
    methods: {
        getStuName(param) {
            console.log(param);
            this.stuName = param.stuName;
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
.dvapp
{
    background-color: aquamarine;
}
</style>
```

[Vue2.0 基础学习目录](/articles/da3d.html)  

完整代码可以在 [GitHub](https://github.com/odinsam/learn-vue2.0)