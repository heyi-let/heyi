---
layout: default
title: 浏览器兼容性
tags: Brower
---


# 浏览器兼容性


览器的兼容性无非是样式兼容性（css），交互兼容性（javascript），浏览器 hack 三个方面。


## 样式兼容性（css）


1. 因为历史原因，不同的浏览器样式存在差异，可以通过 Normalize.css 抹平差异，也可以定制自己的 reset.css。

    例如以前可能需要的，通过通配符选择器，全局重置样式：
    ```
     * { margin: 0; padding: 0; }
    ```
    当然这个是不可取的！

2. 在 CSS3 还没有成为真正的标准时，浏览器厂商就开始支持这些属性的使用了。CSS3 样式语法还存在波动时，浏览器厂商提供了针对浏览器的前缀，直到现在还是有部分的属性需要加上浏览器前缀。在开发过程中我们一般通过 IDE 开发插件、css 预处理器以及前端自动化构建工程帮我们处理。

    浏览器内核与前缀的对应关系如下：
    ```
    内核	    主要代表的浏览器	前缀
    
    Trident	    IE 浏览器	        -ms
    Gecko	    Firefox	            -moz
    Presto	    Opera	            -o
    Webkit	    Chrome 和 Safari	-webkit
    ```
    
    当然，现在 Opera 和 Edge 都使用 Webkit 内核。


3. 在还原设计稿的时候我们常常会需要用到透明属性，所以解决 IE9 以下浏览器不能使用 opacity。

    ```
    opacity: 0.5;
    
    // IE6-IE8 我们习惯使用 filter 滤镜属性来进行实现
    filter: alpha(opacity = 50); 
    
    // IE4-IE9 都支持滤镜写法 progid:DXImageTransform.Microsoft.Alpha(Opacity=xx)
    filter: progid:DXImageTransform.Microsoft.Alpha(style = 0, opacity = 50); 
    
    ```

4. CSS hack

由于不同的浏览器对 CSS 的支持及解析结果不一样，并且不同的 CSS 写法优先级不同。我们就可以根据这个来针对不同的浏览器来写不同的 CSS。比如 IE6 能识别下划线 `_` 和星号 `*`，IE7 能识别星号 `*` ，但不能识别下划线 `_`，而 firefox 两个都不能认识。等等

```
/*类内部hack：*/
.header {_width:100px;}         /* IE6专用*/ 
.header {*+width:100px;}        /* IE7专用*/    
.header {*width:100px;}         /* IE6、IE7共用*/     
.header {width:100px\0;}        /* IE8、IE9共用*/     
.header {width:100px\9;}        /* IE6、IE7、IE8、IE9共用*/     
.header {width:330px\9\0;}      /* IE9专用*/

.test{
    color:red;
    color:red !important;   / Firefox、IE7支持 */
    _color:red;             / IE6支持 */
    *color:red;             / IE6、IE7支持 */
    *+color:red;            / IE7支持 */
    color:red\9;            / IE6、IE7、IE8支持 */
    color:red\0;            / IE8支持 */
}


/*选择器Hack：*/     
*html .header{}     /*IE6*/      
*+html .header{}    /*IE7*/ 
```

5. Chrome 中文界面下默认会将小于 12px 的文本强制按照 12px 显示。

    ```
    .small-font{
        font-size:12px;
    
        transform: scale(0.90);
        transform-origin:0 0;
    
        -ms-transform: scale(0.90);         /* IE 9 */
        -ms-transform-origin:0 0;       /* IE 9 */
    
        -webkit-transform: scale(0.90); /* Safari 和 Chrome */
        -webkit-transform-origin:0 0;   /* Safari 和 Chrome */
    
        -moz-transform: scale(0.90);        /* Firefox */
        -moz-transform-origin:0 0;      /* Firefox */
    
        -o-transform: scale(0.90);      /* Opera */
        -o-transform-origin:0 0;        /* Opera */
    }
    ```

## 交互兼容性（javascript）

1. 事件兼容的问题，我们通常需要会封装一个适配器的方法，过滤事件句柄绑定、移除、冒泡阻止以及默认事件行为处理。

    ```
    var  helper = {}
    
     //绑定事件
     helper.on = function(target, type, handler) {
     	if(target.addEventListener) {
     		target.addEventListener(type, handler, false);
     	} else {
     		target.attachEvent("on" + type,
     			function(event) {
     				return handler.call(target, event);
     		    }, false);
     	}
     };
    
     //取消事件监听
     helper.remove = function(target, type, handler) {
     	if(target.removeEventListener) {
     		target.removeEventListener(type, handler);
     	} else {
     		target.detachEvent("on" + type,
     	    function(event) {
     			return handler.call(target, event);
     		}, true);
         }
     };
    
    ```
2. `new Date()` 构造函数使用 `'2018-07-05'` 是无法被各个浏览器中，使用 `new Date(str)` 来正确生成日期对象的。 正确的用法是 `'2018/07/05'`。


3. 获取 `scrollTop` 通过 `document.documentElement.scrollTop` 兼容非 chrome 浏览器。

    ```
    var scrollTop = document.documentElement.scrollTop||document.body.scrollTop;
    ```



## 浏览器 hack

1. 快速判断 IE 浏览器版本

    ```
     <!--[if IE 8]> ie8 <![endif]-->
     
     <!--[if IE 9]> 骚气的 ie9 浏览器 <![endif]-->
    ```
    
    ```
    使IE5,IE6兼容到IE7模式（推荐）
    
    <!–[if lt IE 7]>
    <script src=”http://ie7-js.googlecode.com/svn/version/2.0(beta)/IE7.js” type=”text/javascript”></script>
    <![endif]–>
    
    使IE5,IE6,IE7兼容到IE8模式
    
    <!–[if lt IE 8]>
    <script src=”http://ie7-js.googlecode.com/svn/version/2.0(beta)/IE8.js” type=”text/javascript”></script>
    <![endif]–>
    
    
    使IE5,IE6,IE7,IE8兼容到IE9模式
    
    <!–[if lt IE 9]>
    <script src=”http://ie7-js.googlecode.com/svn/version/2.1(beta4)/IE9.js”></script>
    <![endif]–>
    
    ```
2. 判断是否是 Safari 浏览器

    ```
     var isSafari = /a/.__proto__=='//';
    ```


3. 判断是否是 Chrome 浏览器

    ```
     var isChrome = Boolean(window.chrome);
    ```













