title: Vue2.0进阶 - 15. vue-router路由
author: OdinSam
tags:
  - router
  - 路由
  - vue2
categories:
  - 前端
  - vue2
abbrlink: cc23
date: 2022-10-20 16:04:00
---
> [Vue2.0进阶学习](/articles/e255.html) 

<!--more-->

#### vue-router路由
1. vue-router是vue的一个插件库，专门用来实现spa应用(单页面应用)
2. 一个路由就是一组映射关系 key-value,key是路径，value是组件(前端)或函数(后端)
3. 创建路由表,多级路由配置child时不需要写父路径 以及 /

```js router/index.js
import VueRouter from 'vue-router';
import Home from '../components/Home.vue';
import About from '../components/About.vue';
export default new VueRouter({
//路由工作的两种模式 默认是hash模式 mode改变模式为history模式
    mode: 'history',
    routes: [
        {
            path: '/home',
            component: Home,
            children: [
            	//不需要写父路径 以及 /  也可以给路由命名 跳转时可以不用path 使用name即可
                { path: 'news', component: News,name:'news',
                	children: [
                        {
                            name: 'detail',
                            //路由使用parmas传参,跳转必须使用name不可以使用path  获取使用$route.params.id获取
                            path: 'detail/:id',
                            component: Detail,
                            //给details组件传递对象，所有数据在details中可以以props的方式接收
                            // props:{a:1,b:2}
                            //如果props是true，则路由收到的params参数会以props的形式传递 即 id 会以props方式传递
                            // props:true
                            // 如果props是函数，则路由同样以props的形式传递参数给组件，但是这可以通过route结构获取到query并传参（此处使用解构赋值的连续写法 先从route中结构获取query，再从query中结构出id，title
                            props({query:{id,title}}){ return {id,title} }
                            
                        }
                    ]
                },
                { path: 'messages', component: Message }
            ]
        },
        {
            path: '/about',
            component: About
        }
    ]
});
```

4. 加载VueRouter插件并加载路由表
```js main.js
import Vue from 'vue';
import App from './App.vue';
import VueRouter from 'vue-router';
import router from './router';

Vue.config.productionTip = false;
Vue.use(VueRouter);
new Vue({
    render: (h) => h(App),
    router
}).$mount('#app');
```
5. 使用route-link实现路由切换 route-view 指定展示位置(被切换掉组件会被销毁)
```js 
<!--一级路由-->
<router-link class="navitem" active-class="navitem-active" to="/home">Home Page</router-link>
<!--一级路由  replace模式：控制路由器跳转时操作浏览器历史记录的模式，默认是push追加记录，replace是替换当前记录。-->
<router-link replace class="navitem" active-class="navitem-active" to="/home">Home Page</router-link>
<!--二级路由-->
<router-link to="/home/news">News</router-link>
<!--route-view展示-->
<router-view></router-view>
<!-- router-link url querystring传参 -->
<router-link :to="`/home/news/detail?id=${n.id}`">{{n.title}}</router-link>
<!-- router-link url params传参 -->
<router-link :to="`/home/news/detail/${n.id}`">{{n.title}}-byid</router-link>
<!-- router-link to对象 query传参 只能用name 不能用path-->
<router-link :to="{
	name:'detail',
    	query:{ id:n.id }
}">
{{n.title}} - query对象形式
</router-link>
<!-- router-link to对象 param传参 只能用name 不能用path-->
<router-link :to="{
	name:'detailid',
    	params:{ id:n.id }
}">
{{n.title}} - params对象形式
</router-link>
```
6. 路由组件一般放在components文件夹，路由组件放在page文件夹
7. 每一个组件都有自己的$route属性，存储自己的路由信息
8. 整个应用只有一个router，可以通过$router获取
9. 也可以不借助router-link实现路由跳转.两种模式：push模式和replace模式

```js
//编程式导航 push模式 query传参
pushShow(n) {
    this.$router.push({
        name:'detail',
        query:{ id:n.id }
    });
},
//编程式导航 replace模式 params传参
replaceShow(n) {
    this.$router.replace({
        name:'detailid',
        params:{ id:n.id }
    });
},

backClick() { this.$router.back(); },	//后退
forwardClick() { this.$router.forward(); },	//前进
goClick() { this.$router.go(2); } //跳转
```

10. 可以使用keep-alive标签包含用来缓存路由组件默认缓存所有组件。:include="[‘组件名’]" 指定需要缓存的路由组件。exclude功能刚好相反
```js
<keep-alive :include="['Message']">
    <router-view></router-view>
</keep-alive>
<keep-alive :exclude="['News']">
    <router-view></router-view>
</keep-alive>
```

11. 当使用keep-alive包含路由组件后，组件不会触发beforeDestroy销毁流程。需要使用路由组件独有的两个生命周期解决 activated()激活、deactivated()失活。

```js
//激活
activated() {
    this.timer=setInterval(() => {
        this.opacity -= 0.01
        if (this.opacity <= 0) this.opacity = 1
        
    }, 16);
},
//失活
deactivated() {
    clearInterval(this.timer)
}
```

12. 路由守卫分为 全局前置路由守卫、全局后置路由守卫、独享路由守卫、组件内守卫
```js router/index.js
//路由守卫分为 全局前置路由守卫、全局后置路由守卫、独享守卫、组件内守卫
//全局前置路由守卫 - 初始化、每次路由切换时被调用
router.beforeEach((to, from, next) => {
    if (to.meta.isAuth) {
        //判断权限
        if (localStorage.getItem('token') === 'odinsam') next();
        else alert('undefind token');
    } else next();
});

//全局后置路由守卫 - 初始化、每次路由切换时被调用
router.afterEach((to, from) => {
    document.title = to.meta.title || 'index';
});
```

13. 独享路由守卫是在加某一个需要控制的路由上.<font color='red'>代码加在路由表需要控制的路由中</font>
```js router/index.js
beforeRouteEnter(to, from, next) {
	// ...
}
```

14. 组件内守卫<font color='red'>代码加在组件里需要控制的路由中</font>

```js
//当路由进入之前   通过路由规则，进入该组件时被调用
beforeRouteEnter (to, from, next) { // ... },
//当路由离开之前    通过路由规则，离开改组件时被调用
beforeRouteLeave (to, from, next) { // ... } 
```

[Vue2.0 基础学习目录](/articles/da3d.html)  

完整代码可以在 [GitHub](https://github.com/odinsam/learn-vue2.0)























