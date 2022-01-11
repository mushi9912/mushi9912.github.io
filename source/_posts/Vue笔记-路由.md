---
title: Vue笔记--路由
date: 2020-02-21 16:19:55
tags:
- Vue
- 入门
categories:
- Vue
- Vuejs
permalink: vue-ronter
---

# 九、Vue路由

## 1、前端渲染和后端渲染的区别

后端渲染：后端处理URL和页面之间的映射关系，服务器直接生产渲染好对应的HTML页，返回给客户端进行展示

前端渲染：浏览器显示的大部分内容都是由前端写的js代码在浏览器中执行，最终渲染出来的网页

+ 前后端分离阶段
  + 随着Ajax的出现，有了前后端分离的开发模式
  
  + 后端只提供API来返回数据，前端通过Ajax获取数据，并且可以通过JavaScript将数据渲染到页面中
  
    <!-- more -->
  
  + 这样做的最大优点是前后端责任的清晰，后端专注于数据上，前端专注于交互和可视化上
  
  + 并且当移动端(IOS/Android)出现后，后端不需要进行任何处理，仍然使用之前的一套API即可
  
  + 目前很多的网站仍然采用这种模式开发
+ 单页面富应用阶段
  + 其实SPA最主要的特点就是前后端分离的基础上加了一层前端路由
  + 也就是前端来维护一套路由规则

## 2、URL的hash和HTML5的history

1. URL的hash

   可以通过更改location.hash的值更改URL，不会进行跳转

2. HTML5的history

   可以通过history.pushState({}, '', "haha")改变URL，实际上它是相当于一个压入栈的操作，history.back()可以进行出栈操作，对URL的变化可以做一个保存

   还可以通过history.replaceState({}, '', "www")改变URL的值，直接改变，浏览器不会有记录保存

   history.go(-1) = history.back()，history.go(1) = history.forward()则等价于history.go(1)，以上三个接口等同于浏览器前进后退

## 3、Vue-router安装与配置方式

1. 通过Vue.use()，安装这个插件

   ```javascript
   import Vue from 'vue'
   Vue.use(VueRouter)
   ```

2. 创建Vue-router对象

   ```javascript
   const routes = [
     // 配置路径和组件之间的映射关系
     {
       // 默认重定向
       path: "",
       redirect: "/home"
     },
     {
       path: '/',
       name: 'Home',
       component: Home
     },
     {
       path: '/about',
       name: 'About',
       // route level code-splitting
       // this generates a separate chunk (about.[hash].js) for this route
       // which is lazy-loaded when the route is visited.
       component: () => import(/* webpackChunkName: "about" */ '../views/About.vue')
     }
   ]
   
   const router = new VueRouter({
     routes,
     mode: "history",
     // active统一配置
     linkActiveClass: "active"
   })
   ```

3. 将Vue-router对象传入到Vue实例中

   ```javascript
   import router from './router'
   
   new Vue({
     router,
     render: h => h(App)
   }).$mount('#app')
   
   ```


## 4、使用router

```html
<template>
  <div id="app">
    <div id="nav">
<!--          tag指定之后渲染成什么标签，replace使用replaceState -->
<!--      <router-link to="/home" active-class="active" replace>Home</router-link> |-->
<!--      <router-link to="/about" active-class="active" replace>About</router-link>-->
      <router-link to="/home" replace>Home</router-link> |
      <router-link to="/about" replace>About</router-link>
      <br>
      <button @click="homeClick()">首页</button>
      <button @click="aboutClick()">关于</button>
    </div>
    <router-view/>
  </div>
</template>
<script>
  export default {
    name: "App",
    methods: {
      homeClick() {
        // 通过代码的方式修改路径
        // this.$router.push("/home")
        this.$router.replace("/home")
        console.log("home")
      },
      aboutClick() {
        // this.$router.push("/about")
        this.$router.replace("/about")
        console.log("about")
      }
    }
  }
</script>
```

## 5、动态路由

```javascript
{
    path: "/user/:id",
    component: User
}
```

```html
<template>
  <div>
    <h2>user界面</h2>
    <p>user内容</p>
    <p>{{$route.params.id}}</p>
  </div>
</template>

<script>
  export default {
    name: "User"
  }
</script>
```

```html
<router-link :to="'/user/' + user" replace>User</router-link>

data() {
      return {
        user: "zhangsan"
      }
}
```

## 6、路由懒加载

当打包加载应用时，JavaScript包会变得非常大，影响页面的加载

如果我们能把不同路由对应的组件分割成不同的代码块，然后当路由被访问的时候才加载对应组件，这样就更加高效了

懒加载的方式

```javascript
// component: () => import(/* webpackChunkName: "about" */ '../views/About.vue')
// 路由的懒加载
component: () => import("../views/About")
```

## 7、路由的嵌套使用

```javascript
{
    path: '/home',
    name: 'Home',
    component: Home,
    children: [
      {
        // 这里注意要写的是news，/home/news也可以
        path: "",
        redirect: "news"
      },
      {
        path: "news",
        component: () => import("../components/News")
      },
      {
        path: "message",
        component: () => import("../components/Message")
      }
    ]
},
```

然后在Home.vue中写入组件

```html
<template>
  <div>
    <h2>这是Home组件</h2>
    <router-link to="/home/news" replace>News</router-link>
    <router-link to="/home/message" replace>Message</router-link>
    <router-view></router-view>
  </div>
</template>

<script>

export default {
  name: 'Home',
}
</script>
```

## 8、传递参数

动态路由方式可以传递参数

还可以通过query来传递参数

```javascript
{
    path: "/profile",
    component: () => import("../components/Profile")
}
```

使用时

```html
<template>
  <div>
    <h2>这是Profile组件</h2>
    <p>{{$route.query.name}}</p>
  </div>
</template>

<script>
  export default {
    name: "Profile"
  }
</script>


<router-link :to="{path: '/profile', query: {name: 'lisi'}}" replace>Profile</router-link>
<!-- 也可以使用方法 -->
profile() {
        this.$router.replace({
          path: "/profile",
          query: {
            name: "lisi"
          }
        })
}
```

## 9、全局导航守卫

监听跳转的过程，并对一些动作做出处理

```javascript
// 全局守卫
// 前置守卫（guard）
router.beforeEach(((to, from, next) => {
  document.title = to.matched[0].meta.title;
  next();
}))
// 后置钩子（hook）
// 不需要next
router.afterEach(((to, from) => {
  
}))


// 并且对每一个路由设置meta元数据（描述数据的数据），例如
  {
    path: "/user/:id",
    meta: {
      title: "用户"
    },
    component: () => import("../views/User")
  },
```

## 10、路由和组件内守卫

```javascript
// 路由内守卫
{
  path: "/user/:id",
  meta: {
    title: "用户"
  },
  component: () => import("../views/User"),
  beforeEnter: (to, from, next) => {
    console.log("进入用户界面")
    next()
  },
},

// 组件内守卫
const Foo = {
  template: `...`,
  beforeRouteEnter (to, from, next) {
    // 在渲染该组件的对应路由被 confirm 前调用
    // 不！能！获取组件实例 `this`
    // 因为当守卫执行前，组件实例还没被创建
  },
  beforeRouteUpdate (to, from, next) {
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    // 可以访问组件实例 `this`
  },
  beforeRouteLeave (to, from, next) {
    // 导航离开该组件的对应路由时调用
    // 可以访问组件实例 `this`
  }
}

```

## 11、keep-alive与代码复用

每次离开首页，点击关于，首页的dom都会被销毁，加上keep-alive可以使它保存在缓存中

```html
<keep-alive>
    <router-view/>
</keep-alive>
```

`include` 和 `exclude` 属性允许组件有条件地缓存。二者都可以用逗号分隔字符串、正则表达式或一个数组来表示：

```html
<!-- 逗号分隔字符串 -->
<keep-alive include="a,b"><!-- 不要随便加空格 -->
  <component :is="view"></component>
</keep-alive>

<!-- 正则表达式 (使用 `v-bind`) -->
<keep-alive :include="/a|b/">
  <component :is="view"></component>
</keep-alive>

<!-- 数组 (使用 `v-bind`) -->
<keep-alive :include="['a', 'b']">
  <component :is="view"></component>
</keep-alive>
```