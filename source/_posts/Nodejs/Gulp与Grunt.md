---
title: Gulp与Grunt
date: 2017-09-20 20:30:33
categories: Node.js
tags:
- 前端框架
- Gulp-Grunt
---

前段时间做个项目，前期顺风顺水，慢慢随着各种需求插入到项目中来，包括后期项目的一些修改，代码冗余度越来越高，页面访问速度也受到一些影响，尤其是对某个功能的修改，可能需要找很久，个人表示很痛苦呀 -_-!
因此决定跳出来去找寻可以不再这么痛苦下去的技术，于是找到了它们！
<!-- more -->

本文主要来介绍 Webpack 和 Gulp 以及 Grunt，它们之间有什么区别？它们如何使用？以及它们是如何为我们项目开发提高效率的？
以下是最近一段时间研究的一些心得体会，拿出来跟大家分享一下~

### 1\. Gulp

> gulp 是基于 Nodejs 的自动任务运行器，能自动化的完成 javascript/coffee/sass/less/html/image/css 等文件的的测试、检查、合并、压缩、格式化、浏览器自动刷新、部署文件生成，并监听文件在改动后重复指定的这些步骤。

通俗来讲，gulp 就是一个处理项目文件的工具，通过 gulp 建立一些任务，当我们对相应文件进行修改之后，会自动触发这些任务，自动的对文件进行了处理。不用我们手动的、反复的去处理这些文件，大大提高了我们的工作效率。

举个栗子：
现在已经开始用 less 来写样式（less 的优点很多，写起来要比 css 快很多，在此不多说，盆友们可以上网了解 less），在 html 使用这些样式，需要用一些工具将. less 文件转换为. css 文件，如果用 Sublime 敲代码的盆友可能知道它有插件自动生成 css，但是. less 和生成的. css 文件在同一目录下。

![](//upload-images.jianshu.io/upload_images/7147355-9af5e6b5b6abb093.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
目录. png 这只是文件比较少的情况，如果文件比较多呢？是不是也是一件很头疼的事情...
下面，上代码带你用 gulp 解决令人头疼的问题 ^ _ ^
前提我们已经创建好一个 nodejs 项目，这里我们安装两个插件: gulp 和 gulp-less(编译 less 文件)
`npm install gulp --save-dev`
`npm install gulp-less --save-dev`

> 新建 gulpfile.js 文件

```
// 说明：gulpfile.js是gulp项目的配置文件，放项目根目录即可。
// 导入工具包
var gulp = require('gulp'), 
    less = require('gulp-less');

// 定义一个testLess任务（自定义任务名称）
gulp.task('testLess', function () {
    gulp.src('public/stylesheets/style.less') //该任务针对的文件
        .pipe(less()) //该任务调用的模块
        .pipe(gulp.dest('public/css')); //将会在public/css下生成style.css
});

```

接下来，我们只需要在命令行输入`gulp testLess`我们就可以在 public/css 下看到生成的 style.css 文件了
but 这显然是我们手动来生成的，我们需要的是让它自动生成，那好吧，现在我们需要一个东西能够来监听 less 文件的修改，然后自动去执行任务

```
gulp.task('testWatch', function () {
    gulp.watch('public/stylesheets/*.less', ['testLess']); //当所有less文件发生改变时，调用testLess任务
});

```

好了，现在我们运行`gulp testWatch`当修改了 less 文件保存，会看到 css 文件也跟着变了 ^ _ ^
补充一点，我们在一个项目里使用 gulp 的时候，我们想启动项目，就自动启动了 testWatch，而不是启动项目之后，我们还要手动启动 testWatch，那么，加上下面代码：

```
gulp.task('default',['testWatch']); // 指定gulp默认启动任务

```

### 2\. Grunt

> Grunt 基于 Node.js ，用 JS 开发，这样就可以借助 Node.js 实现跨系统跨平台的桌面端的操作，例如文件操作等等。此外，Grunt 以及它的插件们，都作为一个 包 ，可以用 NPM 安装进行管理。

Grunt 依赖 Node.js 所以在安装之前确保你安装了 Node.js。然后开始安装 Grunt。
Grunt 可以帮助我们减少很多的工作量，比如：检查每个 JS 文件语法、合并两个 JS 文件、将合并后的 JS 文件压缩、将 SCSS 文件编译等，包括我们上面提到的试用 gulp 将. less 文件转换为. css 文件，grunt 也是可以实现的，下面我们用 grunt 实现 gulp 的转换 less 的功能：

和 gulp 一样，首先我们先安装 grunt 以及 grunt-contrib-less
`npm install grunt --save-dev`
`npm install grunt-contrib-less --save-dev`

> 新建 Gruntfile.js 文件

使用 grunt，主要有三块代码：任务配置代码、插件加载代码、任务注册代码。
任务配置代码就是调用插件配置一下要执行的任务和实现的功能，插件加载代码就是把需要用到的插件加载进来，任务注册代码就是注册一个 task，里面包含刚在前面编写的任务配置代码。
需要注意的是，grunt 的配置代码放到

```
module.exports = function(grunt) {
    ...
};

```

里面，没有为什么...

```
// 1\. 任务配置代码
grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
    //less插件配置
    less: {
        main: {
        options: {
            compress: false,
            yuicompress: false
        },
        files: {
            './public/css/global.css': './public/less/*.less'
        }
    }
    }
});
// pkg 功能是读取 package.json 文件，并把里面的信息获取出来，方便在后面任务中应用

// 2\. 插件加载代码
grunt.loadNpmTasks('grunt-contrib-less');

// 3\. 任务注册代码
grunt.registerTask('lessjs', ['less']);

```

到这一步，我们已经做好 grunt 配置文件了, 控制台输入`grunt lessjs`即可实现同 gulp 一样的效果。
同样的，这一样使我们手动生成的，grunt 能像 gulp 一样自动为我们生成吗？答案是肯定的，grunt 同样也有 watch。
首先安装插件 grunt-contrib-watch
`npm install grunt-contrib-watch --save-dev`
在任务配置代码添加一个 watch 任务：

```
watch: {
    lesses: {
            files: ['./public/less/*.less'],
        tasks: ['less']
    }
}

```

同样需要加载插件代码，任务注册代码：

```
grunt.loadNpmTasks('grunt-contrib-watch');
grunt.registerTask('default', ['less', 'watch']);

```

### 3\. grunt 与 gulp 区别

上面的学习，我们知道 grunt 和 gulp 能实现同样的功能，那么它们有什么区别？分别在什么场景下使用呢？
在我看来，其实 grunt 和 gulp 两个东西的功能是一样的，只不过是任务配置 JS 的语法不同，Gulp 配置文件的写法更加通俗易懂，上手更快。但是 Gulp 的插件感觉不如 Grunt，只能满足基本的工作需要；而 Grunt 官方提供了一些常见的插件，满足大部分日常工作，而且可靠值得信赖，而 Gulp 好像没有太多官方出品，各种插件不太规范。
至于在什么场景下使用哪个？这个看个人需要吧，grunt 远远不止上面写的那么简单，如果 gulp 能满足平时工作需要，可以使用 gulp。
总之，用网友的话说，Grunt 和 Gulp 就像 iPhone 与 Android 一样，一个质量高学习难一点，一个学起来简单但是有点那个，你懂得。