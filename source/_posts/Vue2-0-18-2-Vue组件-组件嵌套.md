title: Vue2.0 - 18.2 Vue组件-组件嵌套
author: OdinSam
tags:
  - vue2
  - component
categories:
  - 前端
  - vue2
abbrlink: ff66
date: 2022-10-18 15:02:00
---
> [vue2.0 基础学习目录](/articles/da3d.html) 

<!--more-->

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>18.2.组件嵌套</title>
        <script src="../js/vue.js"></script>
        <style></style>
    </head>
    <body>
        <!--
            
        -->
        <div id="root">
            <App></App>
        </div>
    </body>
    <script>
        Vue.config.productionTip = false;
        const Student = Vue.extend({
            name: 'Student',
            data() {
                return {
                    stuName: 'odinsam',
                    stuAge: 20
                };
            },
            template: `
                <div style='margin-left:50px;'>
                    <h2>Student组件</h2>
                    <span>姓名：{{stuName}}</span>
                    <span>年龄：{{stuAge}}</span>
                </div>
            `
        });
        const School = {
            name: 'school',
            components: { Student },
            data() {
                return {
                    schName: 'vue学校',
                    schAddress: '南京'
                };
            },
            template: `
                <div>
                    <h2>School组件</h2>
                    <span>课程：{{schName}}</span>
                    <span>地址：{{schAddress}}</span>
                    <div>在校学生</div>
                    <Student></Student>
                </div>
            `
        };
        const Hello = { name: 'Hello', template: `<h2>hello组件</h2>` };
        const App = {
            name: 'App',
            components: { Hello, school: School },
            template: `
                <div>
                    <h2>App组件</h2>
                    <Hello style='margin-left:50px;'></Hello>
                    <school style='margin-left:50px;'></school>
                </div>
            `
        };
        const vm = new Vue({
            el: '#root', // 直接指定vue对应的容器
            components: {
                App
            }
        });
    </script>
</html>
```


[vue2.0 进阶学习的目录](/articles/e255.html)  

完整代码可以在 [GitHub](https://github.com/odinsam/learn-vue2.0)