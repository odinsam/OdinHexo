title: Vue2.0 - 18.3 Vue组件-内置关系
author: OdinSam
tags:
  - components
  - vue2
categories:
  - 前端
  - vue2
abbrlink: 72b9
date: 2022-10-18 15:03:00
---
> [vue2.0 基础学习目录](/articles/da3d.html) 

<!--more-->

1. vue的内置关系 VueComponent.prototype.__proto__ === Vue.prototype
2. 通过这关系，组件实例对象vc可以访问到Vue原型上的属性、方法

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>18.3.vue的内置关系</title>
        <script src="../js/vue.js"></script>
        <style></style>
    </head>
    <body>
        <!--
            1. vue的内置关系  VueComponent.prototype.__proto__ === Vue.prototype
            2. 通过这关系，组件实例对象vc可以访问到Vue原型上的属性、方法
        -->
        <div id="root">
            <Student></Student>
        </div>
    </body>
    <script>
        Vue.config.productionTip = false;
        function Demo() {
            this.a = 1;
            this.b = 2;
        }
        const d = new Demo();
        //显示原型属性
        console.log('Demo.prototype', Demo.prototype);
        //隐式原型属性
        console.log('d.__proto__', d.__proto__);
        // 显示原型属性 和  隐式原型属性 都指向了原型对象

        Demo.prototype.x = 100; //通过显示原型属性操作原型对象，添加x属性 100
        console.log('d.__proto__.x', d.__proto__.x); // 可以输出 100
        console.log(
            'Demo.prototype == d.__proto__',
            Demo.prototype == d.__proto__
        ); //返回true

        Vue.prototype.testProto = 100;
        const Student = {
            name: 'Student',
            data() {
                return { name: 'odinsam', proto: 0 };
            },
            template: `
                <div>
                    <h2>student组件</h2>
                    <div>姓名：{{name}}</div>
                    <div>Vue.prototype.testProto = 100; 获取结果为: {{proto}}</div>
                    <button @click="getVmTestProto">获取vm上的testProto</button>
                </div>`,
            methods: {
                getVmTestProto() {
                    console.log(this.testProto);
                    this.proto = this.testProto;
                }
            }
        };

        const vm = new Vue({
            el: '#root',
            data() {
                return {
                    test: 'test proto'
                };
            },
            components: { Student }
        });
        console.log(
            'Vue.prototype === vm.__proto__',
            Vue.prototype === vm.__proto__
        );
        // VueComponent.prototype.__proto__ === Vue.prototype
        // 组件实例对象vc可以访问到Vue原型上的属性、方法
        console.log(
            'Student.prototype === vm.___proto__',
            Student.prototype === vm.___proto__
        );
    </script>
</html>
```

[vue2.0 进阶学习的目录](/articles/e255.html)  

完整代码可以在 [GitHub](https://github.com/odinsam/learn-vue2.0)