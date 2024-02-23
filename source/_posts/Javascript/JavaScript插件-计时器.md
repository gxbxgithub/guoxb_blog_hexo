---
title: JavaScript插件 - 计时器
date: 2017-08-02 20:50:08
categories: JavaScript
tags:
- js计时器
- js插件
---

js封装的一个计时器的插件，start()开始计时，end()结束计时，返回一次计时的时间。
<!-- more -->


```
;(function(undefined){
	"use strict";
	var _global, timer, time;
	var plugin = {
		start: function(){
			time = 0;
			timer = setInterval(function(){
				 time ++;
				 console.log(time)
			}, 1000);
		},
		end: function(){
			clearInterval(timer);
			return time;
		}
	}
	// 最后将插件对象暴露给全局对象
    _global = (function(){ return this || (0, eval)('this'); }());
    if (typeof module !== "undefined" && module.exports) {
        module.exports = plugin;
    } else if (typeof define === "function" && define.amd) {
        define(function(){return plugin;});
    } else {
        !('plugin' in _global) && (_global.plugin = plugin);
    }
}());
```