---
title: HTML5 拓展jquery scroll事件，支持scroll start 和 scroll stop
categories: HTML5
tags: 
- jquery-scroll
---
javascript里有一个事件是滚动事件，只要拖动滚动条，就会触发事件。
用jquery的话，这个事件scroll 可以查看jquery api ：http://api.jquery.com/scroll/
但scroll 事件有一个缺陷，就是只能判断滚动条滚动，而不能监控滚动条停止滚动时的事件。现用jquery扩展一下scroll 事件,不多说，直接上代码实在点。
<!-- more -->

```
(function(){
 
    var special = jQuery.event.special,
        uid1 = 'D' + (+new Date()),
        uid2 = 'D' + (+new Date() + 1);
 
    special.scrollstart = {
        setup: function() {
 
            var timer,
                handler =  function(evt) {
 
                    var _self = this,
                        _args = arguments;
 
                    if (timer) {
                        clearTimeout(timer);
                    } else {
                        evt.type = 'scrollstart';
                        jQuery.event.handle.apply(_self, _args);
                    }
 
                    timer = setTimeout( function(){
                        timer = null;
                    }, special.scrollstop.latency);
 
                };
 
            jQuery(this).bind('scroll', handler).data(uid1, handler);
 
        },
        teardown: function(){
            jQuery(this).unbind( 'scroll', jQuery(this).data(uid1) );
        }
    };
 
    special.scrollstop = {
        latency: 300,
        setup: function() {
 
            var timer,
                    handler = function(evt) {
 
                    var _self = this,
                        _args = arguments;
 
                    if (timer) {
                        clearTimeout(timer);
                    }
 
                    timer = setTimeout( function(){
 
                        timer = null;
                        evt.type = 'scrollstop';
                        jQuery.event.handle.apply(_self, _args);
 
                    }, special.scrollstop.latency);
 
                };
 
            jQuery(this).bind('scroll', handler).data(uid2, handler);
 
        },
        teardown: function() {
            jQuery(this).unbind( 'scroll', jQuery(this).data(uid2) );
        }
    };
 
})();
```
如果是用高级版本的jquery（如1.9）的话需要将handle改为dispatch;
可以将上面代码保存到一个文件，这相当于一个插件，呵呵。调用方法如下：
```
(function(){
    jQuery(window).bind('scrollstart', function(){
        console.log("start");
    });
 
    jQuery(window).bind('scrollstop', function(e){
        console.log("end");
    });
 
})();
```
