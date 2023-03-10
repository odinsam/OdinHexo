title: Vue2.0 - 13. vue指令
author: OdinSam
tags:
  - v-指令
  - vue2
  - 指令
categories:
  - 前端
  - vue2
abbrlink: 227f
date: 2022-10-17 14:33:00
---
> [vue2.0 基础学习目录](/articles/da3d.html) 

<!--more-->

1. v-text 向所在的节点渲染文本内容，html 会原样输出不解析。 并且节点的原始内容会被替换相对于插值语法在此显得更加灵活
2. v-html 在指定节点中渲染包含 html 结构的内容 容易导致xss攻击
3. v-cloak 当 vue 实例创建完毕并接管容器后会移除 v-cloak 属性。可搭配样式 [v-cloak]{ display:none; } 解决网速过慢页面展示{{xxx}}的问题
4. v-once 所在节点在初始动态渲染后，就视为静止内容，以后数据的改动不会引起 v-once 所在的结构的更新，可用于优化性能。
5. v-pre 跳过所在节点的编译过程，vue不在编译节点内容提升性能
6. v-show 条件渲染 v-show=“bool值或者返回bool类型的表达式” 。 适用于切换较高的场景。DOM 未被移除，只是通过样式 display:none 隐藏
7. v-if 条件渲染 v-if=“bool值或者返回bool类型的表达式” 。 适用于切换较低的场景。DOM 被移除，可能无法获取dom元素
8. v-if 可搭配 v-else-if v-else 但是整体结构不能打断
9. 整体渲染可以使用但是只能使用 v-if 不能使用v-show
10. v-for 列表渲染 遍历数组 v-for=“item in array” :key=“item.id”
11. v-for 列表渲染 遍历对象 v-for="(v,k) in object" :key=“k”
12. v-for 列表渲染 遍历字符串 v-for="(char,index) in string" :key=“index”
13. v-for 列表渲染 遍历数值(次数) v-for="(num,index) in number" :key=“index”
14. key是虚拟dom的标识，当数据发生变化时，会依据key做dom的diff比较运行。如果key使用index在数组进行了逆序的添加、删除等操作破坏了原有顺序会发生bug
15. key一般最好使用id、手机号、身份证号等唯一标识
16. 如果没有逆序操作，使用index作为key的值夜没有问题
17. 自定义指令 示例：
指令名定义时不加 v- ，使用时加 v-
指令名如果是多个单词，需要使用 kebab-case 命名方式，不要用驼峰命名

第一种写法
```js new vue中配置
directives: {
    big(ele, binding) {
        // 第一次调用: 当指令与元素成功绑定  第n次调用: 当指令所在模板被重新解析时
        ele.innerText = binding.value * 10;
    }
    //对象是
}
```

第二种写法

```js
big: {
    bind() {
        console.log(
            '1次调用 - 当指令与元素绑定成功时调用，在内存，页面并没有元素'
        );
    },
    inserted() {
        console.log(
            '1或n次调用 - 指令所在的元素被插入页面时调用'
        );
    },
    update() {
        console.log('1或n次调用: 当指令所在模板被重新解析时');
    }
}
```

全局写法

vm.directive('指令名',配置对象) 或 vm.directive(‘指令名’,回调函数)

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>13.内置指令</title>
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
                ### 数据绑定
                1. v-text 向所在的节点渲染文本内容，html 会原样输出不解析。 并且节点的原始内容会被替换（ {{}} 插值语法在此显得更加灵活 ）
                2. v-html 在指定节点中渲染包含 html 结构的内容 容易导致xss攻击
                3. v-cloak 当 vue 实例创建完毕并接管容器后会移除 v-cloak 属性。可搭配样式 [v-cloak]{ display:none; } 解决网速过慢页面展示{{xxx}}的问题
                4. v-once 所在节点在初始动态渲染后，就视为静止内容，以后数据的改动不会引起 v-once 所在的结构的更新，可用于优化性能。
                5. v-pre 跳过所在节点的编译过程，vue不在编译节点内容提升性能
            -->
            <div>
                <span>v-text</span>
                <span v-text="vtext">span原有内容</span>
                <br />
                <span>v-html</span>
                <span v-html="vhtml"></span>
                <br />
                <span>v-cloak</span>
                <span v-cloak
                    >当 vue 实例创建完毕并接管容器后会移除 v-cloak 属性</span
                >
                <br />
                <span>v-once</span>
                <span v-once>初始onceNum为 {{onceNum}}</span>
                <span>onceNum当前值为 {{onceNum}}</span>
                <button @click="btnAddNum">btnAddNum</button>
                <br />
                <span v-pre
                    >跳过所在节点的编译过程，vue不在编译节点内容提升性能</span
                >
            </div>
            <!--
                ### 条件渲染
                6. v-show 条件渲染  v-show="bool值或者返回bool类型的表达式" 。  适用于切换较高的场景。DOM 未被移除，只是通过样式 display:none 隐藏
                7. v-if 条件渲染   v-if="bool值或者返回bool类型的表达式" 。 适用于切换较低的场景。DOM 被移除，可能无法获取dom元素
                8. v-if 可搭配 v-else-if v-else 但是整体结构不能打断
                9. 整体渲染可以使用 <template></template> 但是只能使用 v-if 不能使用v-show
            -->
            <div>
                <span>条件渲染</span>
                <div v-show="vshow" class="conditiondv">v-show="vshow"</div>
                <button @click="vshowBtnClick">v-show</button><br /><br />
                <div v-if="vif" class="conditiondv">v-if="vif"</div>
                <button @click="vifBtnClick">v-show</button>
                <div v-if="num===1" class="conditiondv">v-if="num===1"</div>
                <!--<div>整体结构不能打断</div>-->
                <div v-else-if="num===2" class="conditiondv">
                    v-else-if="num===2"
                </div>
                <div v-else class="conditiondv">v-else</div>
                <button @click="conditionClick">conditionClick</button>
            </div>
            <!--
                ### 列表渲染
                10. v-for 列表渲染 遍历数组 v-for="item in array" :key="item.id" 
                11. v-for 列表渲染 遍历对象 v-for="(v,k) in object" :key="k"
                12. v-for 列表渲染 遍历字符串 v-for="(char,index) in string" :key="index"
                13. v-for 列表渲染 遍历数值(次数) v-for="(num,index) in number" :key="index"
                14. key是虚拟dom的标识，当数据发生变化时，会依据key做dom的diff比较运行。如果key使用index在数组进行了逆序的添加、删除等操作破坏了原有顺序会发生bug
                15. key一般最好使用id、手机号、身份证号等唯一标识
                16. 如果没有逆序操作，使用index作为key的值夜没有问题

            -->
            <div>
                <span>遍历数组 v-for="item in array" :key="item.id" </span>
                <ul>
                    <li v-for="(user,index) in users" :key="user.id">
                        index:{{index}} id:{{user.id}} 姓名:{{user.name}}
                        年龄:{{user.age}}
                    </li>
                </ul>
                <span>遍历对象 v-for="(v,k) in object" :key="k"</span>
                <ul>
                    <li v-for="(v,k) in users[0]" :key="k">
                        key:{{k}} value:{{v}}
                    </li>
                </ul>
                <span
                    >遍历字符串 v-for="(char,index) in string"
                    :key="index"</span
                >
                <ul>
                    <li v-for="(char,index) in users[0].name" :key="index">
                        char:{{char}} index:{{index}}
                    </li>
                </ul>
                <span
                    >遍历数值(次数) v-for="(num,index) in number"
                    :key="index"</span
                >
                <ul>
                    <li v-for="(number,index) in 5" :key="index">
                        number:{{number}} index:{{index}}
                    </li>
                </ul>
            </div>
            <div>
                <!--
                    17. 自定义指令 示例： <span v-big="customNum"></span>
                        指令名定义时不加 v- ，使用时加 v-
                        指令名如果是多个单词，需要使用 kebab-case 命名方式，不要用驼峰命名
                    第一种写法 
                    new vue中配置
                    directives: {
                        big(ele, binding) {
                            // 第一次调用: 当指令与元素成功绑定  第n次调用: 当指令所在模板被重新解析时
                            ele.innerText = binding.value * 10;
                        }
                        //对象是
                    }
                    第二种写法
                    big: {
                        bind(ele, binding) {
                            console.log(
                                '1次调用 - 当指令与元素绑定成功时调用，在内存，页面并没有元素'
                            );
                        },
                        inserted(ele, binding) {
                            console.log(
                                '1或n次调用 - 指令所在的元素被插入页面时调用'
                            );
                        },
                        update(ele, binding) {
                            console.log('1或n次调用: 当指令所在模板被重新解析时');
                        }
                    }
                    全局写法
                    vm.$directive('指令名',配置对象) 或 vm.$directive('指令名',回调函数)
                -->
                <span>自定义指令</span>
                <span>customNum:{{customNum}}</span>
                <br />
                <span>放大10倍后的值:</span><span v-big="customNum"></span>
                <br />
                <button @click="btnCustomNumAdd">btnCustomNumAdd</button>
            </div>
        </div>
    </body>
    <script>
        Vue.config.productionTip = false;
        const vm = new Vue({
            el: '#root', // 直接指定vue对应的容器
            data() {
                return {
                    customNum: 5,
                    onceNum: 1,
                    num: 1,
                    vtext: '<span>vtext指令</span>',
                    vhtml: '<a href="http://odinsam.com">v-html容易导致xss攻击</a>',
                    vshow: true,
                    vif: true,
                    users: [
                        { id: 'no1', name: 'odinsam1', age: 29 },
                        { id: 'no2', name: 'odinsam2', age: 14 },
                        { id: 'no3', name: 'odinsam3', age: 26 }
                    ]
                };
            },
            methods: {
                vshowBtnClick() {
                    console.log('function vshowBtnClick');
                    this.vshow = !this.vshow;
                },
                vifBtnClick() {
                    console.log('function vifBtnClick');
                    this.vif = !this.vif;
                },
                conditionClick() {
                    this.num++;
                    if (this.num > 3) this.num = 1;
                },
                btnAddNum() {
                    this.onceNum++;
                },
                btnCustomNumAdd() {
                    this.customNum++;
                }
            },
            directives: {
                // big(ele, binding) {
                //     ele.innerText = binding.value * 10;
                // }
                big: {
                    bind(ele, binding) {
                        console.log(
                            '1次调用 - 当指令与元素绑定成功时调用，在内存，页面并没有元素'
                        );
                    },
                    inserted(ele, binding) {
                        console.log(
                            '1或n次调用 - 指令所在的元素被插入页面时调用'
                        );
                    },
                    update(ele, binding) {
                        console.log('1或n次调用: 当指令所在模板被重新解析时');
                    }
                }
            }
        });
    </script>
</html>

```




[vue2.0 进阶学习的目录](/articles/e255.html)  

完整代码可以在 [GitHub](https://github.com/odinsam/learn-vue2.0)