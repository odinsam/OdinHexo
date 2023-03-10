title: Vue2.0 - 16. Filter过滤器
author: OdinSam
tags:
  - vue2
  - Filter
categories:
  - 前端
  - vue2
abbrlink: cb68
date: 2022-10-17 14:46:00
---
> [vue2.0 基础学习目录](/articles/da3d.html) 

<!--more-->

#### Filter过滤器:

1. 对现实的数据进行特性格式化后再显示(适用于一些简单的逻辑处理)
2. 注册过滤器
	```js
    Vue.filter(‘name’,function(){ });
    // 或者
    new Vue(filters:{ filtername([params]){ } })
   ```
3. 使用过滤器
	```js
    {{name | filtername1[ | filtername2]}} 
    // 或者 
    v-bind:属性=“name | filtername1[ | filtername2]”
   ```
4. 过滤器可以接受额外参数，多个过滤器可以串联
5. 并没有改变元数据，只是产生新的对应数据

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>16.过滤器</title>
        <script src="../js/vue.js"></script>
        <style></style>
    </head>
    <body>
        <!--
            过滤器:
            1. 对现实的数据进行特性格式化后再显示(适用于一些简单的逻辑处理)
            2. 注册过滤器 Vue.filter('name',function(){}); 或 new Vue(filters:{ filtername([params]){} })
            3. 使用过滤器 {{name | filtername1[ | filtername2]}} 或者 v-bind:属性="name | filtername1[ | filtername2]"
            4. 过滤器可以接受额外参数，多个过滤器可以串联
            5. 并没有改变元数据，只是产生新的对应数据
        -->
        <div id="root">
            <h2>无参过滤器:{{ username | usernameFormater}}</h2>
            <h2>
                带参过滤器:{{ username | usernameFormaterWithParams('参数')}}
            </h2>
            <h2>
                串联过滤器:{{ username | usernameFormater |
                usernameFormaterWithParams('参数')}}
            </h2>
            <h2>无参全局过滤器:{{ username | globalFilter}}</h2>
            <h2>
                带参全局过滤器:{{ username | globalFilterWithParams('global')}}
            </h2>
            <h2>
                全局过滤器+局部过滤器:{{ username |
                usernameFormaterWithParams('参数') |
                globalFilterWithParams('global')}}
            </h2>
        </div>
    </body>
    <script>
        Vue.config.productionTip = false;
        Vue.filter('globalFilter', function (value) {
            return value + '-无参全局过滤器';
        });
        Vue.filter('globalFilterWithParams', function (value, param) {
            return value + '-带参全局过滤器-' + param;
        });
        const vm = new Vue({
            el: '#root', // 直接指定vue对应的容器
            data() {
                return {
                    username: 'odinsam'
                };
            },
            methods: {},
            filters: {
                usernameFormater(value) {
                    return value + '-无参数';
                },
                usernameFormaterWithParams(value, param) {
                    return value + '-带参数';
                }
            }
        });
    </script>
</html>
```

[vue2.0 进阶学习的目录](/articles/e255.html)  

完整代码可以在 [GitHub](https://github.com/odinsam/learn-vue2.0)