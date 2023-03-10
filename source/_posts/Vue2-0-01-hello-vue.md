---
title: Vue2.0 - 01. hello vue
author: OdinSam
tags:
  - vue
  - 插值语法
categories:
  - 前端
  - vue2
abbrlink: 1d50
date: 2022-10-15 13:58:00
---
> [vue2.0 基础学习目录](/articles/da3d.html) 

<!--more-->

1. 需要创建vue实例，且传入一个配置对象
2. root容器中的代码需要符合html规范，只是加入了vue语法
3. root容器立的代码被称为 vue 模板
4. 插值语法 {{ $1 }} 中的内容需要是 js 表达式，且内容可以直接读取到配置的data中所有的属性
5. vue实例和容器需要时一一对应
6. 一旦 data 中的数据发生变化，页面会自动更新

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>01.hello案例</title>
        <script src="../js/vue.js"></script>
    </head>
    <body>
        <!--
            1.  需要创建vue实例，且传入一个配置对象 
            2.  root容器中的代码需要符合html规范，只是加入了vue语法
            3.  root容器立的代码被称为 vue 模板
            4.  插值语法 {{ $1 }} 中的内容需要是 js 表达式，且内容可以直接读取到配置的data中所有的属性
            5.  vue实例和容器需要时一一对应
            6.  一旦 data 中的数据发生变化，页面会自动更新
        -->
        <div id="root">
            <h1>hello vue</h1>
            <h2>{{name}}</h2>
            <!--当前语法为 vue 的插值语法-->
            <h2>{{age}}</h2>
        </div>
    </body>
    <script>
        //阻止 vue 在启动时生成生产提示
        Vue.config.productionTip = false;
        //创建vue实例
        const vm = new Vue({
            //指定当前vue实例为那个容器服务， css选择器选择对应容器
            el: '#root',
            //定义对应的数据，可以在对应的容器 el 中使用
            data: {
                name: 'odinsam',
                age: 20
            }
        });
    </script>
</html>

```


[vue2.0 进阶学习的目录](/articles/e255.html)  

完整代码可以在 [GitHub](https://github.com/odinsam/learn-vue2.0)