---
title: Vue2.0 - 10. Computed计算属性
author: OdinSam
tags:
  - vue2
  - 计算属性
  - computed
categories:
  - 前端
  - vue2
abbrlink: 8c58
date: 2022-10-15 14:27:00
---
> [vue2.0 基础学习目录](/articles/da3d.html) 

<!--more-->

1. get 当模板读取fullname时，get会被调用，且返回值作为fullname的值
2. get 的调用时机： 1. 初次读取fullname时 2.所有依赖的数据发生变化时 firstName lastName
3. 相对于method实现，如果模板多个位置需要显示fullname时 method的方法会调用多次 而计算属性的get只调用一次
4. 计算属性最终会出现在vm上可以直接使用，例如使用button直接修改fullname
5. 如果修改计算属性，必须有set方法
6. 如果计算属性只有get没有set则可以简写

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>10.计算属性</title>
        <script src="../js/vue.js"></script>
        <style></style>
    </head>
    <body>
        <div id="root">
            <h2>vue 计算属性</h2>
            <span>firstname</span
            ><input type="text" v-model="firstName" /><br /><br />
            <span>lastname</span
            ><input type="text" v-model="lastName" /><br /><br />
            <span>全名:{{fullname}}</span>
            <button @click="btnClick">直接修改计算属性fullname</button>
        </div>
    </body>
    <script>
        Vue.config.productionTip = false;
        const vm = new Vue({
            el: '#root', // 直接指定vue对应的容器
            data() {
                return {
                    firstName: 'odin',
                    lastName: 'sam'
                };
            },
            methods: {
                btnClick() {
                    this.fullname = 'suiji-shu';
                }
            },
            computed: {
                /*
                1. get 当模板读取fullname时，get会被调用，且返回值作为fullname的值
                2. get 的调用时机：  1. 初次读取fullname时  2.所有依赖的数据发生变化时 firstName  lastName
                3. 相对于method实现，如果模板多个位置需要显示fullname时 method的方法会调用多次 而计算属性的get只调用一次
                4. 计算属性最终会出现在vm上可以直接使用，例如使用button直接修改fullname
                5. 如果修改计算属性，必须有set方法
                */
                fullname: {
                    get() {
                        return this.firstName + '-' + this.lastName;
                    },
                    set(value) {
                        const arr = value.split('-');
                        this.firstName = arr[0];
                        this.lastName = arr[1];
                    }
                }
                // 6. 如果计算属性只有get没有set则可以简写
                // fullname(){
                //     return this.firstName + '-' + this.lastName;
                // }
            }
        });
    </script>
</html>

```


[vue2.0 进阶学习的目录](/articles/e255.html)  

完整代码可以在 [GitHub](https://github.com/odinsam/learn-vue2.0)