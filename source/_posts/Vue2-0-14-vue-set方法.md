title: Vue2.0 - 14. vue.set方法
author: OdinSam
tags:
  - vue2
  - vue.set
categories:
  - 前端
  - vue2
abbrlink: 2b60
date: 2022-10-17 14:38:00
---
> [vue2.0 基础学习目录](/articles/da3d.html) 

<!--more-->

#### vue监视数据的原理

1. vue会监视data中所有层次的数据
2. 通过setter实现数据监视，并且要在new vue时就传入需要监视的数据
3. 对象中后续追加的属性，vue默认不做响应式处理
4. 如果需要给添加的属性做响应式处理 需要使用 set api
	Vue.set(data.target,‘prototypeName’/index,‘prototypeValue’)
	this.$set(data.target,‘prototypeName’/index,‘prototypeValue’)
5. 对于数组的更新 需要使用api push、pop、shift、unshift、splice、sort、reverse 或者 Vue.set() | this.$set()
6. Vue.set() | vm.$set() 不能给vm的根数即data直接添加属性
7. 核心是使用 Object.defineProperty 实现了数据劫持

|方法	|使用	|描述|
|:--	|:--	|:--|
|push	|const length = arrayObj. push([item1 [item2 [. . . [itemN ]]]])	|将一个或多个新元素添加到数组结尾，并返回数组新长度|
|pop	|const obj = arrayObj.pop()	|移除最后一个元素并返回该元素值|
|shift	|const obj = arrayObj.shift()	|移除最前一个元素并返回该元素值，数组中元素自动前移|
|unshift	|const length = arrayObj.unshift([item1 [item2 [. . . [itemN ]]]])	|将一个或多个新元素添加到数组开始，数组中的元素自动后移，返回数组新长度|
|splice	|const deleteArr = arrayObj.splice(deletePos,deleteCount,[newItem1,newItem2])	|删除从指定位置deletePos开始的指定数量deleteCount的元素[并添加一个或多个新的元素]，数组形式返回所移除的元素|
|sort	|const sortArr = arrayObj.sort()	|反转元素（最前的排到最后、最后的排到最前），返回数组地址|
|reverse	|const reverseArr = arrayObj.reverse()	|反转元素（最前的排到最后、最后的排到最前），返回数组地址|

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>14.vue_set方法</title>
        <script src="../js/vue.js"></script>
        <style>
            #root {
                display: flex;
                justify-content: space-evenly;
            }
            .conditiondv {
                width: 200px;
                height: 200px;
                background-color: bisque;
            }
            [v-cloak] {
                display: none;
            }
        </style>
    </head>
    <body>
        <div id="root">
            <!--
                vue监视数据的原理
                1. vue会监视data中所有层次的数据
                2. 通过setter实现数据监视，并且要在new vue时就传入需要监视的数据
                3. 对象中后续追加的属性，vue默认不做响应式处理
                4. 如果需要给添加的属性做响应式处理 需要使用 set api
                    Vue.set(data.target,'prototypeName'/index,'prototypeValue')
                    this.$set(data.target,'prototypeName'/index,'prototypeValue')
                5. 对于数组的更新 需要使用api push、pop、shift、unshift、splice、sort、reverse 或者 Vue.set() | this.$set()
                6, Vue.set() | vm.$set() 不能给vm的根数即data直接添加属性
            -->
            <div>
                <span>对象动态添加属性</span>
                <br />
                <span
                    >this.$set(data.object,'prototypeName','prototypeValue')</span
                >
                <span
                    >Vue.set(data.object,'prototypeName','prototypeValue')</span
                >
                <ul>
                    <li v-for="(v,k) in student">
                        <span>key:{{k}}</span>----<span>value:{{v}}</span>
                    </li>
                </ul>
                <button @click="addPrototypeClick">add prototype</button>
            </div>

            <div>
                <span>对象动态修改数组属性</span><br />
                <span
                    >this.$set(data.object,'prototypeName','prototypeValue')</span
                >
                <ul>
                    <li v-for="(v,k) in student">
                        <span>key:{{k}}</span>----<span>value:{{v}}</span>
                    </li>
                </ul>
                <button @click="addHobbyClick">add prototype</button>
                <br />
                <button @click="changeHobbyClick">使用数组的变更方法</button>
                <table border="1">
                    <thead>
                        <td>方法</td>
                        <td>使用</td>
                        <td>描述</td>
                    </thead>
                    <tbody>
                        <tr>
                            <td>push</td>
                            <td>
                                const length = arrayObj. push([item1 [item2 [. .
                                . [itemN ]]]])
                            </td>
                            <td>
                                将一个或多个新元素添加到数组结尾，并返回数组新长度
                            </td>
                        </tr>
                        <tr>
                            <td>pop</td>
                            <td>const obj = arrayObj.pop()</td>
                            <td>移除最后一个元素并返回该元素值</td>
                        </tr>
                        <tr>
                            <td>shift</td>
                            <td>const obj = arrayObj.shift()</td>
                            <td>
                                移除最前一个元素并返回该元素值，数组中元素自动前移
                            </td>
                        </tr>
                        <tr>
                            <td>unshift</td>
                            <td>
                                const length = arrayObj.unshift([item1 [item2 [.
                                . . [itemN ]]]])
                            </td>
                            <td>
                                将一个或多个新元素添加到数组开始，数组中的元素自动后移，返回数组新长度
                            </td>
                        </tr>
                        <tr>
                            <td>splice</td>
                            <td>
                                const deleteArr =
                                arrayObj.splice(deletePos,deleteCount,[newItem1,newItem2])
                            </td>
                            <td>
                                删除从指定位置deletePos开始的指定数量deleteCount的元素[并添加一个或多个新的元素]，数组形式返回所移除的元素
                            </td>
                        </tr>
                        <tr>
                            <td>sort</td>
                            <td>const sortArr = arrayObj.sort()</td>
                            <td>
                                反转元素（最前的排到最后、最后的排到最前），返回数组地址
                            </td>
                        </tr>
                        <tr>
                            <td>reverse</td>
                            <td>const reverseArr = arrayObj.reverse()</td>
                            <td>
                                反转元素（最前的排到最后、最后的排到最前），返回数组地址
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </body>
    <script>
        Vue.config.productionTip = false;
        const vm = new Vue({
            el: '#root', // 直接指定vue对应的容器
            data() {
                return {
                    student: {
                        name: 'odinsam',
                        age: 20,
                        hobby: ['抽烟', '喝酒', '烫头']
                    }
                };
            },
            methods: {
                addPrototypeClick() {
                    console.log('function addPClick');
                    this.$set(this.student, 'sex', '男');
                },
                addHobbyClick() {
                    console.log('function addHobbyClick');
                    this.$set(
                        this.student.hobby,
                        this.student.hobby.length,
                        '学习-' + this.student.hobby.length
                    );
                },
                changeHobbyClick() {
                    console.log('function changeHobbyClick');
                    this.student.hobby.push(
                        'push new hobby-' + this.student.hobby.length
                    );
                }
            }
        });
    </script>
</html>
```

[vue2.0 进阶学习的目录](/articles/e255.html)  

完整代码可以在 [GitHub](https://github.com/odinsam/learn-vue2.0)