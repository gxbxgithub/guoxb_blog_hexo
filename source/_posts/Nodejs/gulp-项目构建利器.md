---
title: gulp-项目构建利器
date: 2017-08-17 20:30:33
categories: Node.js
tags:
- 前端框架
- gulp
---

gulp是什么？

gulp是前端开发过程中对代码进行构建的工具，是自动化项目的构建利器；她不仅能对网站资源进行优化，而且在开发过程中很多重复的任务能够使用正确的工具自动完成；使用她，我们不仅可以很愉快的编写代码，而且大大提高我们的工作效率。
<!-- more -->

gulp是基于Nodejs的自动任务运行器， 她能自动化地完成 javascript/coffee/sass/less/html/image/css 等文件的的测试、检查、合并、压缩、格式化、浏览器自动刷新、部署文件生成，并监听文件在改动后重复指定的这些步骤。在实现上，她借鉴了Unix操作系统的管道（pipe）思想，前一级的输出，直接变成后一级的输入，使得在操作上非常简单。

gulp基于Nodejs，因此我们一切操作在Nodejs项目中操作。

> gulp 之 gulp-less

使用gulp-less插件将less文件编译成css
项目根目录创建gulpfile.js，gulpfile.js是gulp项目的配置文件

```

//导入工具包 require('node_modules里对应模块')

vargulp =require('gulp'),//本地安装gulp所用到的地方

less=require('gulp-less');

//定义一个testLess任务（自定义任务名称）

gulp.task('testLess',function() {

    gulp.src('public/stylesheets/style.less')//该任务针对的文件

    .pipe(less())//该任务调用的模块

    .pipe(gulp.dest('public'));//将会在src/css下生成index.css

});

gulp.task('default',['testLess']);//定义默认任务 elseTask为其他任务，该示例没有定义elseTask任务

```

> gulp 之 gulp-htmlmin

使用gulp-htmlmin压缩html，可以压缩页面javascript、css，去除页面空格、注释，删除多余属性等操作。

```
gulp.task('testHtmlmin', function () {
    var options = {
        removeComments: true,//清除HTML注释
        collapseWhitespace: true,//压缩HTML
        collapseBooleanAttributes: true,//省略布尔属性的值 <input checked="true"/> ==> <input />
        removeEmptyAttributes: true,//删除所有空格作属性值 <input id="" /> ==> <input />
        removeScriptTypeAttributes: true,//删除<script>的type="text/javascript"
        removeStyleLinkTypeAttributes: true,//删除<style>和<link>的type="text/css"
        minifyJS: true,//压缩页面JS
        minifyCSS: true//压缩页面CSS
    };
    gulp.src('views/*.html')
        .pipe(htmlmin(options))
        .pipe(gulp.dest('public/html'));
});

```

> gulp 之 gulp-concat

使用gulp-concat合并javascript文件，减少网络请求。

```
gulp.task('testConcat', function () {
    gulp.src('public/javascripts/*.js')
        .pipe(concat('all.js'))//合并后的文件名
        .pipe(gulp.dest('public'));
});

```

> gulp 之 gulp-clean-css

使用gulp-clean-css压缩css文件，减小文件大小，并给引用url添加版本号避免缓存。

```
// 基本使用
gulp.task('testCssmin', function () {
    gulp.src('public/stylesheets/*.css')
        .pipe(cssmin())
        .pipe(gulp.dest('public'));
});

// 其他参数使用
gulp.task('testCssmin', function () {
    gulp.src('public/stylesheets/*.css')
        .pipe(cssmin({
            advanced: false,//类型：Boolean 默认：true [是否开启高级优化（合并选择器等）]
            compatibility: 'ie7',//保留ie7及以下兼容写法 类型：String 默认：''or'*' [启用兼容模式； 'ie7'：IE7兼容模式，'ie8'：IE8兼容模式，'*'：IE9+兼容模式]
            keepBreaks: true,//类型：Boolean 默认：false [是否保留换行]
            keepSpecialComments: '*'
            //保留所有特殊前缀 当你用autoprefixer生成的浏览器前缀，如果不加这个参数，有可能将会删除你的部分前缀
        }))
        .pipe(gulp.dest('public'));
});

// 给css文件里引用url加版本号（根据引用文件的md5生产版本号）- 避免缓存
// 确保已本地安装gulp-make-css-url-version [cnpm install gulp-make-css-url-version --save-dev]
gulp.task('testCssmin', function () {
    gulp.src('public/stylesheets/*.css')
        .pipe(cssver()) //给css文件里引用文件加版本号（文件MD5）
        .pipe(cssmin())
        .pipe(gulp.dest('public'));
});

// 若想保留注释，这样注释即可：
/*!
   Important comments included in minified output.
*/

```