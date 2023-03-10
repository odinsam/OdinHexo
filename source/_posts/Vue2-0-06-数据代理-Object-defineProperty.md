title: Vue2.0 - 06. 数据代理-Object.defineProperty
author: OdinSam
tags:
  - vue2
  - JavaScript
  - 数据代理
  - defineProperty
categories:
  - 前端
  - vue2
abbrlink: 7c3d
date: 2022-10-15 14:15:00
---
> [vue2.0 基础学习目录](/articles/da3d.html) 

<!--more-->

```js
let user={name:'odinsam',sex:'男'}
Object.defineProperty(user,'age',{value:20,enumerable:true,writable:true,configurable:true})
{name: 'odinsam', sex: '男', age: 20}
for(let key in user){ console.log(`user的key-value    key:${key}    value:${user[key]}`) }
// user的key-value    key:name    value:odinsam
// user的key-value    key:sex    value:男
//user的key-value    key:age    value:20

user.age=30
30
user.age
30
delete user.age
true
for(let key in user){ console.log(`user的key-value    key:${key}    value:${user[key]}`) }
user的key-value    key:name    value:odinsam
user的key-value    key:sex    value:男
```

#### Object.defineProperty的 get set 用法

```js
let number = 20
let user={name:'odinsam',sex:'男'}
Object.defineProperty(user,'age',{ get(){ return number } set(value){ number=value } })
{name: 'odinsam', sex: '男', age: 20}
for(let key in user){ console.log(`user的key-value    key:${key}    value:${user[key]}`) }
// user的key-value    key:name    value:odinsam
// user的key-value    key:sex    value:男
//user的key-value    key:age    value:20

user.age=30
30
user.age
30
delete user.age
true
for(let key in user){ console.log(`user的key-value    key:${key}    value:${user[key]}`) }
user的key-value    key:name    value:odinsam
user的key-value    key:sex    value:男
```

#### 原始的数据代理 obj2通过数据代理获取obj1的x属性

```js
//原始的数据代理
let obj1 = { x: 10 };
let obj2 = { y: 10 };
Object.defineProperty(obj2, 'x', {
    get() {
        return obj1.x;
    },
    set(value) {
        obj1.x = value;
    }
});
```

#### vue中的数据代理

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>06.数据代理-Object.defineProperty</title>
    </head>
    <body></body>
    <script>
        /*
        vue中的数据代理
        1. 通过vm对象来代理data对象中属性的操作 getter setter
        2. 更加方便的操作data中的数据
        3. 通过Object.defineProperty()把data对象中所有的属性添加到vm上
        4. 为每一个添加到wm上的属性都指定 getter、setter方法
        5. 在getter、setter内部操作data中对应的属性
        6. vm._data中的属性不是数据代理而是数据劫持，通过数据劫持监听数据改变从而render页面
        */
    </script>
</html>
```

[vue2.0 进阶学习的目录](/articles/e255.html)  

完整代码可以在 [GitHub](https://github.com/odinsam/learn-vue2.0)