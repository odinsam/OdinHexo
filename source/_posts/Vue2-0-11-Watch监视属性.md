title: Vue2.0 - 11. Watch监视属性
author: OdinSam
tags:
  - vue2
  - watch
  - 监视属性
categories:
  - 前端
  - vue2
abbrlink: bd29
date: 2022-10-15 14:29:00
---
> [vue2.0 基础学习目录](/articles/da3d.html) 

<!--more-->

1.当监视的属性发生变化时，回调函数 handler 会自动调用进行相关操作
2. 监视的属性必须存在，才可以监视
3. 监视属性两种写法
	在new vue时配置watch
	在new vue创建完成后，通过 vm.$watch(‘监视的属性’,{ //监视的配置内容 })
4. 监视多级结构中某个属性的变化 对象.属性 监视
5. watch 默认不检测对象内部值的改变,可以通过 deep:true 进行深度监视
6. 监视属性不光可以监视data中的属性、对象也可以监视计算属性
7. 监视属性可以简写，但代价是不能再配置 immediate、deep

```vue
watch:{
	personState(newValue,oldValue){
		//回调处理函数
	}
}
vm.$watch('personState',function(newValue,oldValue){
	//回调处理函数
})
```
8. computed 计算属性能完成的watch都可以完成。watch可以完成的computed不一定能完成。例如：watch可以进行异步操作
9. 所有被vue管理的函数最好写成普通函数，这样this指向才vm或者组件对象实例
10. 所有不被vue管理的函数（定时器，ajax回调等）最好写成箭头函数，这样this指向才vm或者组件对象实例

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>11.监视属性</title>
        <script src="../js/vue.js"></script>
        <style></style>
    </head>
    <body>
        <div id="root">
            <h2>vue 监视属性</h2>
            <span>人生真的是{{personState}}</span>
            <br /><br />
            <button @click="btnClick">修改人生的状态</button>
            <hr />
            <h3>对象内容x {{obj.x}}</span><br>
            <button @click="btnXClick">btn add</button>
            <hr />
            <h3>对象内容y {{obj.y}}</span><br>
            <button @click="btnYClick">btn add</button>
        </div>
    </body>
    <script>
        Vue.config.productionTip = false;
        const vm = new Vue({
            el: '#root', // 直接指定vue对应的容器
            data() {
                return {
                    state: true,
                    obj: { x: 10, y: 20 }
                };
            },
            methods: {
                btnClick() {
                    this.state = !this.state;
                },
                btnXClick(){
                    this.obj.x ++;
                },
                btnYClick(){
                    this.obj.y++;
                },
            },
            computed: {
                personState() {
                    return this.state ? '大起' : '大落';
                }
            },

            /*
            1. 当监视的属性发生变化时，回调函数 handler 会自动调用进行相关操作
            2. 监视的属性必须存在，才可以监视
            3. 监视属性两种写法
                1. 在new vue时配置watch
                2. 在new vue创建完成后，通过 vm.$watch('监视的属性',{ //监视的配置内容 }) 
            4. 监视多级结构中某个属性的变化 对象.属性 监视
            5. watch 默认不检测对象内部值的改变,可以通过 deep:true 进行深度监视
            6. 监视属性不光可以监视data中的属性、对象也可以监视计算属性
            7. 监视属性可以简写，但代价是不能再配置 immediate、deep
                watch:{
                    personState(newValue,oldValue){
                        //回调处理函数
                    }
                }
                vm.$watch('personState',function(newValue,oldValue){
                    //回调处理函数
                })
            8. computed 计算属性能完成的watch都可以完成。watch可以完成的computed不一定能完成。例如：watch可以进行异步操作
            
            备注：
            所有被vue管理的函数最好写成普通函数，这样this指向才vm或者组件对象实例
            所有不被vue管理的函数（定时器，ajax回调等）最好写成箭头函数，这样this指向才vm或者组件对象实例
            */
            watch: {
                personState:{
                    handler(newValue, oldValue){
                        console.log(
                            `watch: 计算属性 personState 被修改了，原始值是:${oldValue} 新值为:${newValue}`
                        );
                    }
                },
                state: {
                    immediate: true, //初始化时让 handler 调用一次
                    handler(newValue, oldValue) {
                        console.log(
                            `watch: state 被修改了，原始值是:${oldValue} 新值为:${newValue}`
                        );
                    }
                },
                // 监视多级结构中某个属性的变化
                'obj.x':{
                    immediate: true, //初始化时让 handler 调用一次
                    handler(newValue, oldValue) {
                        console.log(
                            `watch: obj.x 被修改了，原始值是:${oldValue} 新值为:${newValue}`
                        );
                    }
                },
                // watch 默认不检测对象内部值的改变,可以通过 deep:true 进行深度监视
                'obj':{
                    deep:true,  //进行深度监视
                    // immediate: true, //初始化时让 handler 调用一次
                    handler(newValue, oldValue) {
                            console.log(`watch: obj 被修改了 obj.x:${newValue.x}    obj.x:${newValue.y}`);
                    }
                },
                //监视属性的简写 

                personState(newValue, oldValue){
                    console.log(
                            `watch: 计算属性 personState 被修改了，原始值是:${oldValue} 新值为:${newValue}`
                        );
                }
            }
        });
        //监视属性的第二种写法 首先保证vm创建完毕
        // vm.$watch('state', {
        //     immediate: true, //初始化时让 handler 调用一次
        //     handler(newValue, oldValue) {
        //         console.log(
        //             `state 被修改了，原始值是:${oldValue} 新值为:${newValue}`
        //         );
        //     }
        // });
    </script>
</html>

```

[vue2.0 进阶学习的目录](/articles/e255.html)  

完整代码可以在 [GitHub](https://github.com/odinsam/learn-vue2.0)