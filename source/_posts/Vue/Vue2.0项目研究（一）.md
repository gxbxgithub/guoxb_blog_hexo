---
title: Vue 2.0项目研究（一）
date: 2017-07-11 21:50:20
categories: Vue
tags:
- 前端框架
- Vue
---

搞懂一门新的技术、新的编程语言，除了研究官方文档，我们还可以研究猿友们开源的项目进行学习、进步。
今天来记录一下这段时间研究的一个开源项目的成果。
<!-- more -->

项目地址：https://github.com/putianxi/putianxi.github.io
项目简介：目的是直观列出全国的莆田系医院

### index.html 文件
首先，来看一下项目index.html文件
```
<body>
    <app-header></app-header>

    <app-layout>
        <app-content slot="app-content"></app-content>
        <app-sidebar slot="app-sidebar"></app-sidebar>
    </app-layout>

    <app-footer></app-footer>

    <script src="dist/build.js"></script>
</body>
```
主要由三部分组成，分别是网站头部`<app-header>`、中间部分`<app-layout>`和网站底部`<app-footer>`。
头部和底部不用多说了，着重研究中间部分。
`<app-layout>`又分为`<app-content>`和`<app-sidebar>`两部分构成，index.html先分析到这里，接下来我们先去看看main.js程序入口。

### main.js 文件

通过查看webpack.config.js文件
```
var path = require('path')
var webpack = require('webpack')

module.exports = {
  entry: './src/main.js'
}
```
main.js作为程序入口，附上一段代码
```
import Vue from 'vue'
import VueResource from 'vue-resource'
import AppHeader from './uilayout/AppHeader.vue'
import AppLayout from './uilayout/AppLayout.vue'
import AppContent from './uilayout/AppContent.vue'
import AppSidebar from './uilayout/AppSidebar.vue'
import AppFooter from './uilayout/AppFooter.vue'

Vue.use(VueResource)

new Vue({
    el: 'body',
    components: {
        AppHeader, AppLayout, AppContent, AppSidebar, AppFooter
    }
})
```
作者main.js文件很简便，引入各个模块的模板文件*.vue。
通过`el: 'body'`将模板渲染到index.html`body`中，`components`中创建五个模板，对应index.html中的三个部分
`AppContent`为高德地图模板，我们暂时不作研究；
主要研究`AppSidebar`，右侧医院列表模板。

### AppSidebar.vue 文件

一个正规的.vue文件一般分为三个部分，分别是`<template></template>`、`<script></script>`、`<style></style>`，html代码写在template中，js代码写在script中，还有css样式写在style中，这三个部分构成完整的.vue模板，下面我们一步步来分析：

#### template
```
<template>
    <area-select-box></area-select-box>

    <div v-if="totalPage">

        <hospital-box v-for="hospital in hospital_show_list"
                      :hospital-info="hospital"
        >
        </hospital-box >

        <div class="columns is-mobile card-pagination">
            <div class="column">
                <a class="button"
                   :class=" {'is-disabled' : currentPage === 1 }"
                   @click="updatePageList('prev')"
                >
                    上一页
                </a>
            </div>
            <div class="column">
                <h2 class="pagination-title">{{ currentPage }} / {{ totalPage }} </h2>
            </div>
            <div class="column">
                <a class="button is-info is-pulled-right"
                   :class=" {'is-disabled' : currentPage === totalPage }" 
                   @click="updatePageList('next')"
                >
                    下一页
                </a>
            </div>
        </div>
    </div>

    <div v-else class="safe-area">
        <h4>
            <span class="icon"><i class="fa fa-check"></i></span>此地区暂时安全
        </h4>
    </div>

</template>
```
其中，`<area-select-box></area-select-box>`是一个小的模板，网站上面的下拉列表；还有`<hospital-box></hospital-box>`也是一个小的模板，作为医院列表的一个小块儿；简单浏览一遍，我们不难发现，Vue页面由很多小的模板组成，而小模板的逻辑都写在它们自己的模板中。