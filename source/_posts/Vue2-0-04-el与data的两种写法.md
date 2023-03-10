title: Vue2.0 - 04. el与data的两种写法
author: OdinSam
tags:
  - el
  - data
  - vue2
categories:
  - 前端
  - vue2
abbrlink: '3899'
date: 2022-10-15 14:10:00
---
> [vue2.0 基础学习目录](/articles/da3d.html) 

<!--more-->

el 与 data 有两种写法
el 的两种写法:

1. 在new vue({ el:'#root' }) 时同时指定el的容器 
2. 在创建vue后 通过实例对象指定el的值  vm.$mount('#root')
data 的两种写法

1. 通过对象的形式 data:{ title:'hello vue' }
2. 使用函数的形式 data() { return { title:'hello vue'  } }
后期使用函数式组件时，data必须使用函数形式

#### <font color='red'>重点注意:</font>

<font color='red'>由vue管理的函数(例如 data 的函数式写法)，一定不能写箭头函数，否则this指向的实例就会是window对象</font>

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>04.el与data的两种写法</title>
        <script src="../js/vue.js"></script>
    </head>
    <body>
        <div id="root">
            <h1>通过vue的实例对象挂载数据:{{title}}</h1>
        </div>
    </body>
    <script>
        /*
            el 与 data 有两种写法
            el 的两种写法:
                1. 在new vue({ el:'#root' }) 时同时指定el的容器 
                2. 在创建vue后 通过实例对象指定el的值  vm.$mount('#root')

            data 的两种写法
                1. 通过对象的形式 data:{ title:'hello vue' }
                2. 使用函数的形式 data() { return { title:'hello vue'  } }
            后期使用函数式组件时，data必须使用函数形式

            重点注意: 由vue管理的函数(例如 data 的函数式写法)，一定不能写箭头函数，否则this指向的实例就会是window对象
        */
        Vue.config.productionTip = false;
        // 写法1
        const vm = new Vue({
            // el: '#root',    // 直接指定vue对应的容器
            // 使用data对象形式
            // data: {
            //     title: 'hello vue',
            //     url: 'http://www.odinsam.com'
            // }
            // 使用函数式
            data() {
                return {
                    title: 'hello vue !!'
                };
            }
        });
        // 写法2
        vm.$mount('#root');
    </script>
</html>
```

[vue2.0 进阶学习的目录](/articles/e255.html)  

完整代码可以在 [GitHub](https://github.com/odinsam/learn-vue2.0)