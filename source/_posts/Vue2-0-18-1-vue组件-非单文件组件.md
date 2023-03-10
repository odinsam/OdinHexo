title: Vue2.0 - 18.1 vue组件-非单文件组件
author: OdinSam
tags:
  - component
  - vue2
categories:
  - 前端
  - vue2
abbrlink: 26da
date: 2022-10-18 14:54:00
---
> [vue2.0 基础学习目录](/articles/da3d.html) 

<!--more-->

#### 1. 使用vue组件的步骤:

	1. 创建组件 单文件组件、非单位件组件
   2. 注册组件
	3. 使用组件（组件标签）
	定义组件:
    1. 使用Vue.extend({options}) 或者 const cpt = {options} 创建,其中options和new Vue({options})传入的options略有区别组件的 options 不需要el， 最终的el由new Vue({options})决定
    2. data必须写成函数，避免组件被服用时，数据存在引用关系。示例代码如下:
    
	```js
    let data={a:1,b:2}
    const x1 = data
    const x2 = data
    // 以上代码会导致数据存在引用关系，当x1修改 a或b 的值，x2也会改变

    function data(){
        return {a:1,b:2}
    }
    const x1 = data()
    const x2 = data()
    //通过函数形式可以巧妙的避开上边代码的问题  x1修改 x2不会改变
   ```

2. 使用 template: 来配置组件结构
3. 关于组件名
	一个单词组成: 首字母小写 student 或者 首字母大写 School
	多个单词组成: kebab-case命名 my-school 或者 CamelCase大驼峰 MyStudent(需要vue脚手架支持)
	不可以使用 html已有元素名称 h1 div span 等
	尽量使用 name 配置项指定组件在开发者工具中呈现的名字

#### 2. 注册组件

局部注册 new Vue({ components:{ 组件名:组件 } })
全局注册 Vue.component(‘组件名’，组件)

#### 3. 使用组件

<组件 /> 或者 <组件></组件>
<组件 /> 不使用脚手架，会导致后续组件无法渲染

#### 4. 说明:

1. 组件本质是VueComponent的构造函数，并且是由Vue.extend生成的
2. 当使用组件时，vue会帮助我们创建组件的对象实例（自动调用方法new VueComponent(options)创建组件实例）
3. 每次调用Vue.extend返回的都是一个全新的VueComponent
4. this指向：
	1.组件配置中：data函数、methods中的函数、watch中的函数、computed中的函数都指向VueComponent对象实例
	2.new Vue配置中：data函数、methods中的函数、watch中的函数、computed中的函数都指向Vue对象实例
    
```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>18.1.非单文件组件</title>
        <script src="../js/vue.js"></script>
        <style></style>
    </head>
    <body>
        <!--
            使用vue组件的步骤:
            1. 创建组件 单文件组件、非单位件组件
            2. 注册组件
            3. 使用组件（组件标签）

            定义组件:
            1. 使用Vue.extend({options}) 或者 const cpt = {options} 创建,其中options和new Vue({options})传入的options略有区别
                组件的 options 不需要el， 最终的el由new Vue({options})决定
                data必须写成函数，避免组件被服用时，数据存在引用关系。示例代码如下:
                let data={a:1,b:2}
                const x1 = data
                const x2 = data
                // 以上代码会导致数据存在引用关系，当x1修改 a或b 的值，x2也会改变

                function data(){
                    return {a:1,b:2}
                }
                const x1 = data()
                const x2 = data()
                //通过函数形式可以巧妙的避开上边代码的问题  x1修改 x2不会改变


            2. 使用 template:`` 来配置组件结构
            3. 关于组件名
                一个单词组成: 首字母小写 student 或者 首字母大写 School
                多个单词组成: kebab-case命名  my-school  或者 CamelCase大驼峰  MyStudent(需要vue脚手架支持)
                不可以使用 html已有元素名称  h1 div  span 等
                尽量使用 name 配置项指定组件在开发者工具中呈现的名字 


            注册组件
            1. 局部注册  new Vue({ components:{ 组件名:组件 } })
            2. 全局注册  Vue.component('组件名'，组件)

            使用组件
            1. <组件 /> 或者 <组件></组件>
            2. <组件 /> 不使用脚手架，会导致后续组件无法渲染

            说明:
            1. 组件本质是VueComponent的构造函数，并且是由Vue.extend生成的
            2. 当使用组件时，vue会帮助我们创建组件的对象实例（自动调用方法new VueComponent(options)创建组件实例）
            3. 每次调用Vue.extend返回的都是一个全新的VueComponent
            4. this指向：  
                1.组件配置中：data函数、methods中的函数、watch中的函数、computed中的函数都指向VueComponent对象实例
                2.new Vue配置中：data函数、methods中的函数、watch中的函数、computed中的函数都指向Vue对象实例
        -->
        <div id="root">
            <school></school>
            <Student></Student>
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
                <div>
                    <h2>Student组件</h2>
                    <span>姓名：{{stuName}}</span>
                    <span>年龄：{{stuAge}}</span>
                </div>
            `
        });
        const School = {
            name: 'school',
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
                </div>
            `
        };
        const vm = new Vue({
            el: '#root', // 直接指定vue对应的容器
            components: {
                Student,
                school: School
            }
        });
    </script>
</html>

```

[vue2.0 进阶学习的目录](/articles/e255.html)  

完整代码可以在 [GitHub](https://github.com/odinsam/learn-vue2.0)