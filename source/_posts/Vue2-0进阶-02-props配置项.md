title: Vue2.0进阶 - 02. props配置项
author: OdinSam
tags:
  - vue2
  - props
categories:
  - 前端
  - vue2
abbrlink: '40e6'
date: 2022-10-18 15:25:00
---
> [Vue2.0进阶学习](/articles/e255.html) 

<!--more-->

#### props配置项
1. 让组件接收外部传过来的数据 &lt;Student stuName=“odinsam” :age=“10”&gt;&lt;/Student&gt;
2. 如果使用props传递数组、对象、方法等数据时，应该是 &lt;Student :getStudentName=“getStudentName”&gt;&lt;/Student&gt;通过props传递函数getStudentName
接收数据
```js 简单接收
props: ['stuName', 'age']
```

```js 接收的同时规范数据类型
props: {
    stuName: String,
    age: Number,
}
```

```js 接收的同时 限制数据类型、限制必要性、指定默认值
props: {
    stuName: {
        type: String,
        required:true,
    },
    age: {
        type: Number,
        required:true,
    },
    className: {
        type: String,
        default:"1班",
    }
}
```
3. props是只读的，Vue底层会检测对props的修改。如果进行了修改，会发出警告。如果业务需要修改，name复制props内容到data中，修改data中的数据

完整代码
```js app.vue
<template>
  <div class="dvapp">
    <!--
        props配置项
        1. 让组件接收外部传过来的数据 <Student stuName="odinsam" :age="10"></Student>
        2. 接收数据
            简单接收 props: ['stuName', 'age']

            接收的同时限制数据类型
            props: {
                stuName: String,
                age: Number,
            }

            接收的同时 限制数据类型、限制必要性、指定默认值
            props: {
                stuName: {
                    type: String,
                    required:true,
                },
                age: {
                    type: Number,
                    required:true,
                },
                className: {
                    type: String,
                    default:"1班",
                }
            }
        3. props是制度的，Vue底层会检测对props的修改。如果进行了修改，会发出警告。如果业务需要修改，name复制props内容到data中，修改data中的数据
    -->
    <h2>app组件</h2>
    <Student stuName="odinsam" :age="10"></Student>
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

```js student.vue
<template>
    <div class="studv">
        <h2>Student组件</h2>
        <span>姓名：{{stuName}}</span><br />
        <span>年龄：{{myAge}}</span><br />
        <span>班级：{{className}}</span><br />
        <button @click="changeAge">修改姓名</button>
    </div>
</template>

<script>
export default {
    data() {
        return {
            myAge:this.age
        }
    },
    methods: {
        changeAge() {
            this.myAge++
        }
    },
    // 简单接收
    // props: ['stuName', 'age']

    // 接收的同时限制数据类型
    /*
    props: {
        stuName: String,
        age: Number,
    }
     */

    props: {
        stuName: {
            type: String,
            required:true,
        },
        age: {
            type: Number,
            required:true,
        },
        className: {
            type: String,
            default:"1班",
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