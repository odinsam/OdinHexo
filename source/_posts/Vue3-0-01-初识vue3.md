title: Vue3.0 - 01. 初识vue3
author: OdinSam
tags:
  - vue3
categories:
  - 前端
  - vue3
abbrlink: '2774'
date: 2022-10-20 16:13:00
---
> [Vue3.0 学习系列](/articles/151c.html) 

<!--more-->

#### 1. main.js 与vue2的区别
```js
//引入的不在是vue的构造函数，而是一个名为createApp的工厂函数
import { createApp } from 'vue';
import App from './App.vue';

//createApp(app)创建应用实例对象app，类似vue2中的vm，但比vm更轻
createApp(App).mount('#app');
```

#### 2. setup函数
1. vue3.0中的衣蛾新配置项setup.setup是所有组合api的基础
2. 组件中所有用都的 数据、方法等都要配置在setup中。
3. setup函数的返回值有两种，
如果返回一个对象，则对象中的属性、方法等在模板中军可以直接使用
如果返回一个渲染函数，则可以自定渲染内容
4. setup不能是一个async函数，因为返回值不再是return的对象而是promise。末班看不到reutnr对象中的属性。
5. 尽量不要vue3与vue2混用。如果重名以vue3为主
6. ref可以将string、number转换为引用实现的实例对象(refimpl).从而实现数据的响应式操作.模板在解析是如果显示的数据是refimpl对象则会自动获取value的值.对象类型的数据使用了reactive函数实现了响应式.
7. reactive只能将对象类型的数据改变为响应式数据。并且在获取时不需要再通过value而可以直接获取 let 代理对象=reactive(代理对象).reactive定义的响应式数据是深层次的。是基于es6的proxy实现，通过代理对象操作源对象内部数据进行操作。
8. reactive定义的响应式数据可以直接修改对象的属性、通过数组的下标操作数组.具体实现原理如下:
```js
let person = {name:'odinsam'}
let p = new Proxy(person,{
    // 读取属性时调用
    get(target,propName) { return Reflect.get(target,propName) }
    // 新增、修改时调用
    set(target,propName,value) { Reflect.set(target,propName,value) }
    // 删除属性时调用
    deleteProperty(target,propName){ return Reflect.deleteProperty(target,propName) }
})
```

备注:
	ref通常用来定义基本类型数据，reactive通常定义对象类型的数据
	ref通过Object.defineProperty()的get、set方法来实现响应式，reactive通过Proxy来实现响应式，并通过Reflect操作源对象内部的数据
	ref定义的数据需要.value获取，模板中获取不需要.value.reactive定义的数据，操作读取都不需要.value
    
9. setup在beforeCreate之前执行，且this是undefind
10. setup的参数
	props:值为对象，包含：组件外部传递过来且组件内部申明接收了的属性
	context:
	attrs:值为对象，包含：组件外部传递过来，但没有在props配置中申明的属性。相当于vue2的 attrs slots:收到的插槽内容，相当于 this.slots
	emit: 分发自定义事件的函数 相当于 this.$emit
    
```js student.vue
<template>
    <div>
        <span>props信息:{{propa}}</span>
        <p></p>
        <span>姓名:{{stuName}}</span>
        <p></p>
        <span>班级:{{stuInfo1.className}}</span><span>年龄:{{stuInfo1.stuAge}}</span>
        <p></p>
        <span>性别:{{stuInfo2.sex}}</span><span>地址:{{stuInfo2.address}}</span>
        <p></p>
        <span>爱好:{{stuInfo2.hobby}}</span>
        <p></p>
        <span v-show="stuInfo2.newProto">新属性:{{stuInfo2.newProto}}</span>
        <p></p>
        <slot name="slotStu"></slot>
        <p></p>
        <button @click="btnClick">edit</button>
        <button @click="addProto">add Prototype</button>
        <button @click="deleteProto">delete Prototype</button>
        <button @click="customEventClick">emit触发自定义事件</button>
    </div>
  </template>
  
  <script>
    // ref 可以将基本数据类型或者对象改变为响应式数据 let name = ref('odinsam')
    // reactive 只能将对象类型的数据改变为响应式数据 let stuInfo = reactive({sex:'男',address:'beijing'})
    // let 代理对象=reactive(代理对象).reactive定义的响应式数据是深层次的。是基于es6的proxy实现，通过代理对象操作源对象内部数据进行操作。
    // 实现原理
    /**
            let person = {name:'odinsam'}
            let p = new Proxy(person,{
                // 读取属性时调用
                get(target,propName) { return Reflect.get(target,propName) }
                // 新增、修改时调用
                set(target,propName,value) { Reflect.set(target,propName,value) }
                // 删除属性时调用
                deleteProperty(target,propName){ return Reflect.deleteProperty(target,propName) }
            })
    */
    //备注:
    // 1. ref通常用来定义基本类型数据，reactive通常定义对象类型的数据
    // 2. ref通过Object.defineProperty()的get、set方法来实现响应式，reactive通过Proxy来实现响应式，并通过Reflect操作源对象内部的数据
    // 3. ref定义的数据需要.value获取，模板中获取不需要.value.reactive定义的数据，操作读取都不需要.value
  import { ref,reactive } from 'vue'
  export default {
    name: 'Student',
    //接收父组件通过props传递的数据
    props: ['propa'],
    //申明父组件传递的自定义事件。不申明会有警告
    emit: ['cmpEvent'],
    /**
    props:值为对象，包含：组件外部传递过来且组件内部申明接收了的属性
    context:
    	attrs:值为对象，包含：组件外部传递过来，但没有在props配置中申明的属性。相当于vue2的 $attrs
        slot:收到的插槽内容，相当于 this.$slots
        emit: 分发自定义事件的函数 相当于 this.$emit
     */
    setup(props,context) {
        //将数据转换为refimpl对象从而实现响应式编程
        let stuName = ref('odinsam')
        let stuInfo1 = ref({ className: '一年级', stuAge: 19 })
        let stuInfo2 = reactive({ sex: '男', address: 'beijing',hobby:['学习','游戏','看电影'] })
        function btnClick() {
            console.log(stuName);
            //获取refimpl对象的值通过value属性
            stuName.value = 'changeName'
            stuInfo1.value.className = '二年级'
            stuInfo1.value.stuAge = 20
            //reactive定义的代理对象在获取时不需要再通过value而可以直接获取 
            stuInfo2.sex = '女'
            stuInfo2.address = 'nanjing'
        }
        // vue3可以直接修改对象的属性、通过数组的下标操作数组.
        function addProto() {
            //vue2 中 需要 this.$set(对象,'属性名',属性的值) 操作    
            stuInfo2.newProto = '新属性'
        }
        function deleteProto() {
            //vue2 中 需要 this.$delete(对象,'属性名') 操作    
            delete stuInfo2.newProto
        }
        function customEventClick() {
            //触发自定义事件
            context.emit('cmpEvent', {customEventParam:'自定义方法的参数'});   
        }
        return {
            stuName,
            stuInfo1,
            stuInfo2,
            btnClick,
            addProto,
            deleteProto,
            customEventClick
        }
    }
  }
  </script>
  
  <style>
  </style>
```
```js app.vue
<template>
    <div>
        <h2>app组件</h2>
        <p></p>
        <div>自定义方法的参数:{{cstmParam.customEventParam}}</div>
        <Student propa="propa传值" @cmpEvent="cmpEvent">
            <!--vue3中 需要使用 v-slot:插槽名 的方式定义  不能使用 slot='操作名' 的vue2形式-->
            <template v-slot:slotStu>
                <div style="background-color:aquamarine">
                    <span>插槽内容</span>   
                </div>
            </template>
        </student>
    </div>
</template>

<script>
import { reactive } from '@vue/reactivity';
import Student from './components/Student.vue';
export default {
    name: 'App',
    components: {
        Student
    },
    setup() {
        let cstmParam = reactive({})
        function cmpEvent(param) {
            cstmParam['customEventParam'] = param['customEventParam']
        }
        return {
            cstmParam,
            cmpEvent,
        }
    }
}
</script>

<style>
</style>
```


[Vue2.0 基础学习目录](/articles/da3d.html)  

[Vue2.0进阶学习](/articles/e255.html) 

完整代码可以在 [GitHub](https://github.com/odinsam/learn-vue2.0)