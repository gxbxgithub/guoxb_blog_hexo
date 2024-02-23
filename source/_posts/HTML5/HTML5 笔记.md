---
title: HTML5 笔记
categories: HTML5
tags: 
  - H5笔记
---

学习html5以来，遇到很多项目中的问题，以下是个人总结的知识点，这些点在项目开发中经常遇到，希望对大家有所帮助~
<!-- more -->

### 1. `li` 撑不起`ul` 可以在`ul`最下面添加如下代码
```
<div style="height:0;overflow:hidden;clear:both;"></div>
// 或者
.clearfix { *zoom: 1;  }
.clearfix:after { content: ‘’; display: block; clear: both; }
```

### 2. 浏览器兼容
```
-ms-box-sizing:border-box;   //IE
-moz-box-sizing:border-box;   //Firefox
-webkit-box-sizing:border-box;
box-sizing:border-box;
```

### 3. 移动端css—加上如下代码
```
button,select{background:transparent; -webkit-appearance:none;}
input, button, select, textarea{outline:none !important;}
textarea{resize: none;}
input:focus, button:focus, select:focus, textarea:focus{box-shadow:none !important; outline:none !important;}

// 取消手机点击屏幕时，会出现的灰块
html,body{-webkit-text-size-adjust: 100%;-webkit-tap-highlight-color: rgba(0, 0, 0, 0);}
```

### 4. 文本显示不全，后面显示…
```
display: block; overflow:hidden;word-break:keep-all;white-space:nowrap;text-overflow:ellipsis;

<code>换行
white-space: normal; word-break: break-all;
```

### 5. 百分比-`px`
```
width: -webkit-calc( 100% - 70px ); width: calc( 100% - 70px );
```

### 6. 文本太长省略。。。
```
overflow: hidden;  text-overflow:ellipsis; white-space:nowrap;
```

### 7. `textarea` 高度随文本变化
```
<textarea style="overflow: hidden; font-style: normal; font-size: 14px; line-height: normal; " rows="1" cols="30" onfocus="javascript:ResizeTextarea(this,1);" onclick="javascript:ResizeTextarea(this,1);" onkeyup="javascript:ResizeTextarea(this,1);"></textarea>	

textarea{min-height: 33px; max-height: 65px; padding: 0; border: 0; width: -webkit-calc( 100% - 20px ); width: calc( 100% - 20px ); margin: 6px 0; border-radius: 5px; padding: 7px;}

<script>
var agt = navigator.userAgent.toLowerCase();
var is_op = (agt.indexOf("opera") != -1);
var is_ie = (agt.indexOf("msie") != -1) && document.all && !is_op;
function ResizeTextarea(a,row){
    if(!a){return}
    if(!row)
        row=5;
    var b=a.value.split("\n");
    var c=is_ie?1:0;
    c+=b.length;
    var d=a.cols;
    if(d<=20){d=40}
    for(var e=0;e<b.length;e++){
        if(b[e].length>=d){
            c+=Math.ceil(b[e].length/d)
        }
    }
    c=Math.max(c,row);
    if(c!=a.rows){
        a.rows=c;
    }
}
</script>
```

### 8. 手机端打开h5页面，点击a标签会有阴影的问题
```
对a标签 添加样式 -webkit-tap-highlight-color:transparent;
```

### 9. 模糊 -【毛玻璃】
```
.blur {	
    filter: url(blur.svg#blur); /* FireFox, Chrome, Opera */
    -webkit-filter: blur(10px); /* Chrome, Opera */
    -moz-filter: blur(10px);
    -ms-filter: blur(10px);    
    filter: blur(10px);
 filter:progid:DXImageTransform.Microsoft.Blur(PixelRadius=10, MakeShadow=false); /* IE6~IE9 */
}
```

### 10. 修复H5 `Button` 颜色异常问题
```
appearance:square-button;
-moz-appearance:square-button; /* Firefox */
-webkit-appearance:square-button; /* Safari 和 Chrome */
```

### 11. 获取url后面的参数值
```
function UrlValue(name) {
    var reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)", "i");
    var r = window.location.search.substr(1).match(reg);
    if (r != null) return unescape(r[2]); return null;
}

// 中文乱码问题

function UrlValue(name) {
    var reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)", "i");
    var r = window.location.search.substr(1).match(reg);
    if (r != null) return decodeURI(r[2]); return "";
}
```

### 12. 获取第几个到第几个元素
```
$('.cls_list ul li').slice(li_length-3, li_length).css('border-bottom', 'none');
```

### 13. 渐变 
```
#grad1 {
    height: 200px;
    background: -webkit-linear-gradient(red, blue); /* Safari 5.1 - 6.0 */
    background: -o-linear-gradient(red, blue); /* Opera 11.1 - 12.0 */
    background: -moz-linear-gradient(red, blue); /* Firefox 3.6 - 15 */
    background: linear-gradient(red, blue); /* 标准的语法（必须放在最后） */
}
```

### 14. 置顶
```
function scrollToTop(){
    $('body,html').animate({ scrollTop: 0 }, 200);
}
```

### 15. `$(document).on()`在Safari浏览器不能用的问题解决方案
```
点击的元素加样式cursor: pointer;
```

### 16. Safari浏览器点击数字自动拨打电话
```
<meta name="format-detection" content="telephone=no,email=no,adress=no">
```

### 17. 引入自定义字体
```
@font-face {
	font-family: 'FZZZHJT';
	src: url('../font/FZZZHJT.eot');
	src:
	    url('../font/FZZZHJT.eot?#font-spider') format('embedded-opentype'),
	    /*url('../font/FZZDHJT.woff') format('woff'),*/
	    url('../font/FZZZHJT.ttf') format('truetype'),
	    url('../font/FZZZHJT.svg') format('svg');
  	font-weight: normal;
  	font-style: normal;
}
```

### 18. 与客户端交互
```
var ios_bridge = null;
connectWebViewJavascriptBridge(function(bridge) {
    /* Init your app here */
    if(bridge) {
        ios_bridge = bridge;
    }
});
function connectWebViewJavascriptBridge(callback){
    if (window.WebViewJavascriptBridge) {
        callback(WebViewJavascriptBridge)
    } else {
        document.addEventListener('WebViewJavascriptBridgeReady', function() {
            callback(WebViewJavascriptBridge)
        }, false)
    }
}
// 开启试管之旅
function openTubeTour(){
	if(window.JavascriptInterface && window.JavascriptInterface[func_tag]){
		window.JavascriptInterface.gotoDoctor(tag_id, tag_name)
	}
	if(ios_bridge){
		ios_bridge.send({tag:"gotoTopic", topic_id:topic_id});
	}
}
```

### 19. jquery 拷贝数组
```
[].concat(new_arr);
```

### 20. 透明度
```
filter:alpha(opacity=70); -moz-opacity:0.7; opacity:0.7;
```

### 21. 中心平移法解决划线粗的问题
```
var ctx = document.getElementById('scroll_canvas').getContext('2d');
ctx.translate(0.5,0.5);
```

### 22. 整屏滚动
```
jquery.fullPage.min.js 这个插件可以实现一屏一屏的滚动
```

### 23. 滚动停止监听方法
```
var timer;

window.onscroll=function(){
    if(timer)
    clearTimeout(timer)
    
    timer = setTimeout(function(){
        console.log('scrolling ends..')
        //TODO some codes here
    },1000)
}
```

### 24. 隐藏滚动条
```
.class::-webkit-scrollbar {
    background: transparent;
    width: 0px;
    height: 0;
}
// https://segmentfault.com/q/1010000000204586
```

### 25. 对象深拷贝
```
function deepCopy(source) { 
    var result = {};
    for (var key in source) {
        result[key] = typeof source[key] === 'object'? deepCopy(source[key]): source[key];
    } 
    return result; 
}
```

### 26. sel查找控件
```
<input type="range" min="10" max="1000" step="10" value="500" data-rangeslider>

var $document   = $(document);
var selector    = '[data-rangeslider]';
var $inputRange = $(selector);
```

### 27. 将html代码中`<img>`替换
```
var text_html = document.getElementById('saytext').innerHTML;
text_html = text_html.replace(/<img [^>]*src=['"]([^'"]+)[^>]*>/gi,function(match, capture){
var newStr = '[em_' + capture.substring(capture.indexOf('/') + 1, capture.indexOf('.')) + ']';
return newStr;
});
```

### 28. 输入框文本输入实时监听事件-属性
```
onpropertychange="handle();" oninput="handle();"
```

### 29. 判断obj是否是字符串类型
```
((typeof obj!='string')&& obj.constructor!=String)
```

### 30. `trim()` 去除空格
```
String.prototype.trim = function(){
	return this.replace(/(^\s+)|(\s+$)/g, "");
}
```

### 31.
```
/*
num 要产生多少个随机数
from 产生随机数的最小值
to 产生随机数的最大值
*/
 
function createRandom2(num , from , to)
{
    var arr=[];
    var json={};
    while(arr.length<num)
    {
        //产生单个随机数
        var ranNum=Math.ceil(Math.random()*(to-from))+from;
        //通过判断json对象的索引值是否存在 来标记 是否重复
        if(!json[ranNum])
        {
            json[ranNum]=1;
            arr.push(ranNum);
        }
         
    }
    return arr;
     
     
}
alert(createRandom2(10,0,50));//生成10个从0-50之间不重复的随机数
```

### 32. 图片懒加载—只加载可视区域图片
```
var aImages = document.images; //获取id为SB的文档内所有的图片
loadImg(aImages);
window.onscroll = function() { //滚动条滚动触发
    loadImg(aImages);
};
//getBoundingClientRect 是图片懒加载的核心
function loadImg(arr) {
    for(var i = 0, len = arr.length; i < len; i++) {
        if(arr[i].getBoundingClientRect().top < document.documentElement.clientHeight && !arr[i].isLoad) {
            arr[i].isLoad = true; //图片显示标志位
            //arr[i].style.cssText = "opacity: 0;";
            (function(i) {
                setTimeout(function() {
                    if(arr[i].dataset) { //兼容不支持data的浏览器
                        aftLoadImg(arr[i], arr[i].dataset.imgurl);
                    } else {
                        aftLoadImg(arr[i], arr[i].getAttribute("data-imgurl"));
                    }
                    arr[i].style.cssText = "transition: 1s; opacity: 1;" //相当于fadein
                }, 500)
            })(i);
        }
    }
}

function aftLoadImg(obj, url) {
    var oImg = new Image();
    oImg.onload = function() {
        obj.src = oImg.src; //下载完成后将该图片赋给目标obj目标对象
    }
    oImg.src = url; //oImg对象先下载该图像
}
```

### 33. `transform left 50% top 50%` 居中
```
-webkit-transform: translate(-50%, -50%);
-moz-transform: translate(-50%, -50%);
-ms-transform: translate(-50%, -50%);
-o-transform: translate(-50%, -50%);
transform: translate(-50%, -50%);
```

### 34. `input type=number` 右侧会有上下箭头
```
input::-webkit-outer-spin-button, input::-webkit-inner-spin-button{-webkit-appearance: none;}
input[type='number']{-moz-appearance: textfield;}
```

### 35. `iframe` 加载完毕 再执行
```
$(document).ready(function(e){    
	var iframe = document.getElementById("online_intro");      
    if (iframe.attachEvent) {
        iframe.attachEvent("onload", function() {      
            //iframe加载完成后你需要进行的操作
            iframe_load = true;
            if (room_load) {
            	send_room_info();
            }
        });      
    } else {      
        iframe.onload = function() {      
            //iframe加载完成后你需要进行的操作
            iframe_load = true;  
            if (room_load) {
            	send_room_info();
            }
        };      
    }
})
```

### 36. `replace` 全部替换
```
var reg = new RegExp(",","g");//g,表示全部替换。
a.replace(reg,"-");
```

### 37. 数组去重
```
Array.prototype.unique = function(){
    var res = [];
    var json = {};
    for(var i = 0; i < this.length; i++){
        if(!json[this[i]]){
            res.push(this[i]);
            json[this[i]] = 1;
        }
    }
    return res;
}
```

### 38. css清除浮动的方法
```
.clearfix:before, .clearfix:after {
    content: " ";
    display: table;
}
.clearfix:after {clear: both;}
.clearfix: {zoom: 1}
```

### 39. 弹框点击空白区域隐藏
```
document.onmousedown = function(ev){
    var ev = ev || window.event;
    ev = $(ev.target);
    if(ev.attr('class') == 'alert'){
        ev.hide();
    }
}
```

### 40. 遇到h5页面图片点击自动放大的问题
```
pointer-events: none;
```

### 41. 弹框点击空白区域隐藏
```
// 弹框点击空白区域隐藏
document.ontouchend = function(ev){
    var ev = ev || window.event;
    ev = $(ev.target);
    if(ev.attr('itemprop') == 'contentUrl'){
        ev.stopPropagation();
    }    
}
```

### 42. `canvas` 将两张图片合并
```
function drawMyCanvas(code){
        var canvas = document.getElementById("myCanvas");
        var context = canvas.getContext("2d");
        var myImage = new Image();
        myImage.src = 'img/background.png';
        myImage.crossOrigin = 'Anonymous';
        myImage.onload = function(){
            context.drawImage(myImage, 0, 0, 640, 1136);
            // draw 文本
            context.font = "26px Arial";
            context.fillStyle = '#fd6265';
            context.fillText(code, 355, 378);

            var imgUri = canvas.toDataURL("image/png"); // 获取生成的图片的url
            $('#myImg').attr('src', imgUri);
            //$('#myImg').parent().attr({'href': imgUri, download: 'hyb_four_anni'});
            return;
            // 生成一个a元素
            var a = document.createElement('a');
            // 将a的download属性设置为我们想要下载的图片名称，若name不存在则使用‘下载图片名称’作为默认名称
            a.download = 'hyb_four_anni.png';
            // 将生成的URL设置为a.href属性
            a.href = imgUri;
            // 创建一个单击事件
            var event = document.createEvent('MouseEvents');//new TouchEvent('click');
            //event.initMouseEvent('click');
            event.initMouseEvent('click',true,false,window,0,0,0,0,0,false,false,false,false,0,null);
            // 触发a的单击事件
            a.dispatchEvent(event);
        }
}
```

### 43. 上传base64图片
```
首先获取base64图片<img>
/** 
    * 根据base64 内容 取得 bolb 
    * 
    * @param dataURI 
    * @returns Blob 
    */
    function getBlobBydataURI(dataURI,type) { 
        var binary = atob(dataURI.split(',')[1]); 
        var array = []; 
        for(var i = 0; i < binary.length; i++) { 
        array.push(binary.charCodeAt(i)); 
        } 
        return new Blob([new Uint8Array(array)], {type:type }); 
    } 

function downloadImg(){
        //base64 转 blob 
        var $Blob= getBlobBydataURI(document.getElementsByTagName("img")[0].src,'image/png'); 
        var formData = new FormData(); 
        formData.append("files", $Blob ,"file_"+Date.parse(new Date())+".png"); 

        $.ajax({
            url:'/api/image/upload?uid=123',
            type:'post',
            data:formData,
            async: true,
            crossDomain: true,
            contentType: false,
            processData: false,
            success:function(data){
                console.log(data);
            },
            error:function(error){
                console.log(error);
            }
        })

        return;

        //组建XMLHttpRequest 上传文件 
        var request = new XMLHttpRequest(); 
        //上传连接地址 
        request.open("POST", "/api/image/upload?uid=123"); 
        request.onreadystatechange = function() { 
            if (request.readyState == 4) 
            { 
                if(request.status == 200){ 
                    console.log("上传成功"); 
                }else{ 
                    console.log("上传失败,检查上传地址是否正确"); 
                } 
            } 
        } 
        request.send(formData); 
    }
```

### 44. 获取元素定位
```
var input = document.getElementById('J_MyInput'); // 根据ID取得元素对象
var sizeObj = input.getBoundingClientRect(); // 取得元素距离窗口的绝对位置
// 窗口的滚动偏移（垂直、水平）
var bodyOffset = {top: document.body.scrollTop, left: document.body.scrollLeft};
 
// 元素相对于页面的绝对位置 = 窗口滚动偏移 + 元素相对于窗口的绝对位置
var inputOffsetTop = sizeObj.top + bodyOffset.top; // 距顶部
var inputOffsetLeft = sizeObj.left + bodyOffset.left; // 距左侧
```

### 45. h5字体变细
```
font-family: \\5FAE\8F6F\96C5\9ED1,Tahoma,Helvetica,Arial,\\5B8B\4F53,sans-serif;
-moz-font-feature-settings: "kern";
-webkit-font-smoothing: antialiased;
-moz-osx-font-smoothing: grayscale;
```