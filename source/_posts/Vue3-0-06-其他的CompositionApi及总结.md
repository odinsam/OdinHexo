title: Vue3.0 - 06. 其他的CompositionApi及总结
author: OdinSam
tags:
  - vue3
  - compositionApi
categories:
  - 前端
  - vue3
abbrlink: '9754'
date: 2022-10-21 16:30:00
---
> [Vue3.0 学习系列](/articles/151c.html) 

<!--more-->

#### 1. shallowReactive
shallowReactive只处理对象最外层属性的响应式，浅层次的。
#### 2. shallowRef
shallowRef只处理基本数据类型的响应式，不处理对象类型的响应式。如果是基本数据类型，shallowRef与ref没有区别。但shallowRef不处理对象类型即，如果是对象类型shallowRef则不会处理为响应式数据
使用时机
如果一个对象数据，结构比较深，但变化时只是外层属性变化，则可以使用shallowReactive。
如果一个对象谁，后续功能不会修改该对象中的属性，而是生成新的对象来替换，则可以使用shallowRef
#### 3. readonly 与 shallowReadonly
readonly 会让一个响应式数据变为只读 - 深只读
shallowReadonly 让一个响应式数据变为只读 - 浅只读
当不希望数据修改时可以使用
#### 4. toRaw 与 markRaw
toRow 可以将响应式数据变为原始数据 const x = toRaw(person) toRaw的参数需要是refImpl类型的数据
markRaw 标记一个对象，使其永远不会再变为响应式对象
#### 5. customRef
创建一个自定义的ref，并对其依赖项跟踪和更新触发进行显式控制。
```js student.vue
<template>
    <div>
        <h2>Student组件</h2><br/>
        <input type="text" v-model="msg"><br/>
        <span>数据：{{msg}}</span>
    </div>
</template>
  
<script>
import { customRef } from 'vue'
/**

*/
export default {
    name: 'Student',
    setup(props, context) {
        function myref(value) {
            let timer;
            return customRef((track, trigger) => {
                return {
                    get() {
                        // 通知vue跟踪value的变化
                        track();
                        return value;
                    },
                    set(newValue) {
                        clearTimeout(timer)
                        timer = setInterval(() => {
                            value = newValue;
                            // 通知vue重新解析模板
                            trigger()
                        }, 500);
                    }
                }
            })
        }
        let msg = myref('')
        return {
            msg
        }
    }
}
</script>
  
<style>
</style>
```

#### 6. provider与inject
provider和inject可以实现组件间数据通信的方式。
在父组件中使用provider提供数据，在后代组件（子孙组件）中使用inject来获取数据
```js
//父组件
let obj = { x:1,y:2 };
provider('data',obj)
//子组件
let obj = inject('data')
```
#### 7. 对响应式数据的判断
isRef 检查一个值是否是一个 ref 对象
isReactvie 检查一个对象是否是 reactive创建的响应式代理
isReadonly 检查一个对象是否有 readonly 创建的只读对象
isProxy 检查一个对象是否是 reactive或者 readonly 方法创建的代理
#### 8. CompositionApi的优势
传统的OptionApi中，新增或者修改一个需求，需要在 data、methods、computed里修改或添加对应的代码
CompositionApi的优势在于我们可以更优雅的阻止我们的代码、函数。让相关功能的代码更加有序的组织自一起。


[Vue2.0 基础学习目录](/articles/da3d.html)  

[Vue2.0进阶学习](/articles/e255.html) 

完整代码可以在 [GitHub](https://github.com/odinsam/learn-vue2.0)