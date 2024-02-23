---
title: Vue 2.0 （一）
date: 2017-06-21 21:01:20
categories: Vue
tags:
- 前端框架
- Vue
---

项目搭建不做赘述，可以参考[Vue2.0 新手完全填坑攻略——从环境搭建到发布][1]
我们项目用的是：vue2.0 + vue-cli + webpack + vue-router2.0 + vue-resource1.3.4 
<!-- more -->

### 简单介绍目录结构

`node_modules` 是项目中安装的依赖模块 
`App.vue` App.vue组件
`main.js` 入口文件
`.babelrc` babel编译参数，vue开发需要babel编译
`index.html` 主页
`package.json` 项目文件，记载着一些命令和依赖还有简要的项目描述信息

其中package.json 文件是项目配置文件，其中3个节点我们注意一下：
```
dependencies：项目发布时的依赖
devDependencies：项目开发时的依赖
scripts：编译项目的一些命令
```

### App.vue

这是一个标准的vue组件，包含三个部分，一个是模板，一个是script，一个是样式
```
<template>
  <div id="app">
    // ...
  </div>
</template>

<script>
    // ... 
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

### vue-router

将组件(components)映射到路由(routes)，然后告诉 vue-router 在哪里渲染它们。下面是个基本例子：
#### HTML
```
<div id="app">
  <h1>Hello App!</h1>
  <p>
    <!-- 使用 router-link 组件来导航. -->
    <!-- 通过传入 `to` 属性指定链接. -->
    <!-- <router-link> 默认会被渲染成一个 `<a>` 标签 -->
    <router-link to="/foo">Go to Foo</router-link>
    <router-link to="/bar">Go to Bar</router-link>
  </p>
  <!-- 路由出口 -->
  <!-- 路由匹配到的组件将渲染在这里 -->
  <router-view></router-view>
</div>
```

#### JavaScript
```
// 首先导入Vue和VueRouter，要调用 Vue.use(VueRouter)
// 1. 定义（路由）组件。
// 可以从其他文件 import 进来
const Foo = { template: '<div>foo</div>' }
const Bar = { template: '<div>bar</div>' }

// 2. 定义路由
// 每个路由应该映射一个组件。 其中"component" 可以是
// 通过 Vue.extend() 创建的组件构造器，
// 或者，只是一个组件配置对象。
// 我们晚点再讨论嵌套路由。
const routes = [
  { path: '/foo', component: Foo },
  { path: '/bar', component: Bar }
]

// 3. 创建 router 实例，然后传 `routes` 配置
// 你还可以传别的配置参数, 不过先这么简单着吧。
const router = new VueRouter({
  routes // （缩写）相当于 routes: routes
})

// 4. 创建和挂载根实例。
// 记得要通过 router 配置参数注入路由，
// 从而让整个应用都有路由功能
const app = new Vue({
  router
}).$mount('#app')
```
需要注意的是，当`<router-link>`对应的路由匹配成功，将自动设置class属性值`.router-link-active`，成高亮状态。

### 动态路由匹配

我们有一个 User 组件，对于所有 ID 各不相同的用户，都要使用这个组件来渲染。那么，我们可以在 vue-router 的路由路径中使用『动态路径参数』（dynamic segment）来达到这个效果：

```
const User = {
  template: '<div>User</div>'
}

const router = new VueRouter({
  routes: [
    // 动态路径参数 以冒号开头
    { path: '/user/:id', component: User }
  ]
})
```
像 /user/foo 和 /user/bar 都将映射到相同的路由。
路由参数使用冒号标记。当匹配到一个路由时，参数值会被设置到`this.$route.params`。

```
模式                            匹配路径          
/user/:username                 /user/evan                  
/user/:username/post/:post_id   /user/evan/post/123

$route.params
{ username: 'evan' }
{ username: 'evan', post_id: 123}
```

另外，$route.query 可设置获取 URL 中的参数

#### 响应路由参数的变化

从/user/foo 导航到 /user/bar , 原来的组件实例会被复用，这样更高效，不过也意味着组件的生命周期钩子不会再被调用。如果想让程序响应，该怎么办？
是问题就有解决的办法：可以简单的watch（监测变化）$route对象：
```
const User = {
  template: '<div>User</div>',
  watch: {
    '$route' (to, from) {
        // 。。。
    }
  }
}
```