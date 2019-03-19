---
title: JQuery&jQuery插件
date: 2017-01-06 23:23:32
tags: [JQuery, 前端]
---

1. `$(document).ready(function(){})`  
 简写为:`$(function(){})`  
 *注意: DOM节点全部加载进去才会触发(并不是资源加载完以后,所以不等价于window.onload)*
2. 获取元素  `$('选择器')`*有第二个参数,默认document,即从document下获取*,返回一个jq对象
3. .click(为事件绑定事件处理函数)

	`$("div")[1]`会返回原生元素(`<div></div>`)  
	`$("div").eq(1)`会返回jq对象,可以直接用jq方法

	data和attr  
	1. 传一个参数就是读取  
	2. 传两个参数就是设置

## 源码分析: 
```javascript
/*
	在浏览器中运行jq文件，window是存在的
	运行在nodejs中，是没有window的

	把window传进去是为了性能考虑的
*/

(function(global,factory){
	factory(global);
})(window,function (window,noGlobal){
	//jQuery的源码	
	console.log("我是源码，我被执行了");
	//noGlobal的值为undefined
	//在非严格模式下，undefined值可以被改写
	var undefined = 123;
	var abc;
	console.log(abc ===  noGlobal );
	
	//分析整体架构
	var jQuery = function (){
		//return new 	jQuery();  //会循环调用
		return new jQuery.fn.init();
	};
	//jQuery.fn是jQuery.prototype的别名
	jQuery.fn = jQuery.prototype = {
		constructor:jQuery,
		each:function (){
			console.log("我是each");
		}
	}
	//可以定义另一个函数，作为构造函数
	var init = jQuery.fn.init = function (){
		
	}
	//让init原型和jQuery的原型共同引用一个对象
	init.prototype = jQuery.fn;
	window.jQuery = window.$ = jQuery;
});
//console.log( new $().css );
//在jquery中用的时候，无new的实例化过程
//这样调用each方法，调用的是init.prototype上的方法
$().each();
```

链式调用的原理是每个方法返回的都是jquery对象

## 关于JQuery插件
#### 基于两个方法: 

1. $.extend([deep], target, object1, [objectN])
	- 写了target是正常功能,即把obj扩展到target里  
	- 不写target的话就是扩展到自己的命名空间里
	
	自己封装的深复制: 
	
	```javascript
	function extend(obj){
		//先判断传入参数的类型是数组还是对象  [] {}
		var target = obj.constructor === Array ? [] : {};
	
	
		for( var attr in obj ){
			//判断一下obj[attr]是否是对象类型的，如果是，把obj[attr]也赋值一份
	
			if(typeof obj[attr] === "object"){
				//递归地复制找到的任何对象
				target[attr] = extend(obj[attr]);
			}else{
				target[attr] = obj[attr];
			}
		}
	
		return target;
	
	}
	```


2. $().extend(obj)

#### 书写格式: 

- 匿名函数自执行,把jQuery传进去

	```javascript
	(function ($){
		$.fn.extend({
			drag1:function (){
				console.log("drag1");	
			},
			drag2:function (){
				console.log("drag2");	
			},
			a:10
		})

		$.extend({
			dialog1:function (){
				console.log("dialog1");	
			},
			dialog2:function (){
				console.log("dialog2");	
			}
		})
	})(jQuery);
	```
	
#### 一个插件样例(功能: 高亮一组jqDOM元素): 
声明:

```javascript
//闭包限定命名空间
(function ($) {
    $.fn.extend({
        "highLight": function (options) {
            //检测用户传进来的参数是否合法
            if (!isValid(options))
                return this;
            var opts = $.extend({}, defaluts, options); //使用jQuery.extend 覆盖插件默认参数
            return this.each(function () {  //这里的this 就是 jQuery对象。这里return 为了支持链式调用
                //遍历所有的要高亮的dom,当调用 highLight()插件的是一个集合的时候。
                var $this = $(this); //获取当前dom 的 jQuery对象，这里的this是当前循环的dom
                //根据参数来设置 dom的样式
                $this.css({
                    backgroundColor: opts.background,
                    color: opts.foreground
                });
                //格式化高亮文本
                var markup = $this.html();
                markup = $.fn.highLight.format(markup);
                $this.html(markup);
            });

        }
    });
    //默认参数
    var defaluts = {
        foreground: 'red',
        background: 'yellow'
    };
    //公共的格式化 方法. 默认是加粗，用户可以通过覆盖该方法达到不同的格式化效果。
    $.fn.highLight.format = function (str) {
        return "<strong>" + str + "</strong>";
    }
    //私有方法，检测参数是否合法
    function isValid(options) {
        return !options || (options && typeof options === "object") ? true : false;
    }
})(window.jQuery);
```

调用

```javascript
//调用
        //调用者覆盖 插件暴露的共公方法
        $.fn.highLight.format = function (txt) {
            return "<em>" + txt + "</em>"
        }
        $(function () {
            $("p").highLight({ foreground: 'orange', background: '#ccc' }); //调用自定义 高亮插件
        });
```