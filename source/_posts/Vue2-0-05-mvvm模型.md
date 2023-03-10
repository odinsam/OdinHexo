title: Vue2.0 - 05. mvvm模型
author: OdinSam
tags:
  - mvvm
  - vue2
categories:
  - 前端
  - vue2
abbrlink: a38e
date: 2022-10-15 14:12:00
---
> [vue2.0 基础学习目录](/articles/da3d.html) 

<!--more-->

![mvvm](/images/a38e/05.mvvm.png)

M - 模型 即 data 中的数据
V - 视图 即 模板 
VM - viewModel 即 vue的实例对象

##### data bindings 数据以对象的形式存储在data中，通过databindings将数据绑定在 view 页面中

##### view页面改变，通过 dom listeners 修改 data中的数据

##### data中所有的属性，最后都出现在vm立

##### vm所有的属性即vue原型的属性，在vue模板中都可以直接使用



[vue2.0 进阶学习的目录](/articles/e255.html)  

完整代码可以在 [GitHub](https://github.com/odinsam/learn-vue2.0)