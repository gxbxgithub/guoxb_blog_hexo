---
title: iOS开发总结 - Xcode常见错误
date: 2019-01-07 12:18:36
categories: iOS
tags: 
- Xcode
---

>iOS开发中，出现各种错误，针对这些错误，这篇文章给出了正确的解决方案，并总结下来帮助遇到困难的朋友。文章将由[夜雨寒](https://www.jianshu.com/u/46ae5ab6420e)维护并持续更新，有不对的地方，可以在下面评论区提出来~
<!-- more -->

#### 1.查询异常
* `"directory not found for option '-L/…"`
   >表示查询 Library 的时候出现异常
   解决方法：
   依次 Project -> targets -> Build Setting -> Library Search Paths删除里面的路径
    
* `"directory not found for option '-F/..."`
   >表示查询 Framework 的时候出现异常
   解决方法：
   依次 Project -> targets -> Build Setting -> Framework Search Paths删除里面的路径
   
#### 2.library not found for -lstdc++.6.0.9
>更新Xcode 10.1之后，项目运行报这个错误：
library not found for -lstdc++.6.0.9
意思是找不到 libstdc++.6.0.9，由于iOS12移除了libstdc++.6.0.9，取消了6.0.9的支持

解决办法：

1. 打开Build Phases -> Link Binary With Libraries，删除libstdc++.6.0.9依赖，如果项目中有用到c++6.0.9的，可以添加Libc++.tdb；
如果依赖c++6.0.9的SDK是第三方SDK等待第三方更新解决
2. 在Build Phases -> Build Settings 找到Other Linker Flags，将里面的-l "stdc++.6.0.9"删除