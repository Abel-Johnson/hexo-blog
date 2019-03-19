---
title: 闭包在for循环中的应用
date: 2017-01-06 23:23:32
tags: [JavaScript, 前端]
---


```javascript
var divs = document.getElementsByTagName("div");
for (var i = 0; i < divs.length; i++) {
	tab(i);
}
function tab(n){
	divs[n].onclick = function(){
		alert(n);
	}
}
```
--
注意
 
1 闭包允许内层函数引用父函数中的变量，但是该变量是最终值

```javascript
/**  
 * <body>  
 * <ul>  
 *     <li>one</li>  
 *     <li>two</li>  
 *     <li>three</li>  
 *     <li>one</li>  
 * </ul>  
 */
     
var lists = document.getElementsByTagName('li');  
for(var i = 0 , len = lists.length ; i < len ; i++){  
    lists[ i ].onmouseover = function(){  
        alert(i);      
    };  
}
```
你会发现当鼠标移过每一个`<li>`元素时，总是弹出4，而不是我们期待的元素下标。这是为什么呢？  
1. 当mouseover事件调用监听函数时，首先在匿名函数（ function(){ alert(i); }）内部查找是否定义了 i，结果是没有定义；  
2. 因此它会向上查找，查找结果是已经定义了，并且i的值是4（循环后的i值）；  
3. 所以，最终每次弹出的都是4。  

**解决方法一：**

```javascript
var lists = document.getElementsByTagName('li');  
for(var i = 0 , len = lists.length ; i < len ; i++){  
    (function(index){  
        lists[ index ].onmouseover = function(){  
            alert(index);      
        };
    })(i);  
}
```
**解决方法二：**

```javascript
function eventListener(list, index){  //相比于最开始的那种方法,这个函数更纯,不依赖外部变量
    list.onmouseover = function(){  
        alert(index);  
    };  
}  
var lists = document.getElementsByTagName('li');  
for(var i = 0 , len = lists.length ; i < len ; i++){  
    eventListener(lists[ i ] , i);  
}
```

---
2 内存泄露  
使用闭包十分容易造成浏览器的内存泄露(内存占用过量)，严重情况下会是浏览器挂死


