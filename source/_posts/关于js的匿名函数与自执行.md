---
title: 关于js的匿名函数与自执行
date: 2017-01-06 23:23:32
tags: [DOM,HTML,JavaScript,  前端]
---
## 基本格式: 
`(function(x,y){alert(x+y)})(a,b)`  
第一个括号创建了一个匿名函数,  
第二个括号调用了它并传入了参数  
括号包起来就是表达式,不包括号就只是一个函数声明..这也就是为什么(funcrion(){})()可以被执行,而function(){}()却会报错

> (1). 首先, 要清楚两者的区别:     (function {// code})是表达式, function {// code}是函数声明.     
       (2). 其次, js"预编译"的特点:     js在"预编译"阶段, 会解释函数声明, 但却会忽略表式.     
       (3). 当js执行到function() {//code}();时, 由于function() {//code}在"预编译"阶段已经被解释过, js会跳过function(){//code}, 试图去执行();, 故会报错; 
           
>    当js执行到(function {// code})();时, 由于(function {// code})是表达式, js会去对它求解得到返回值, 由于返回值是一 个函数, 故而遇到();时, 便会被执行.
> 
>    另外， 函数转换为表达式的方法并不一定要靠分组操作符()，我们还可以用void操作符，~操作符，!操作符……
> 


## 闭包(closure)
**优点**: *可以大大减少我们的代码量,使我们的代码看上去更加清晰*

**概念**: 函数的嵌套,根据作用域链,内层函数可以使用外层函数的变量,即使外层函数已经执行完毕  
**典型代码示例**: 

```javascript
function checkClosure(){  
    var str = 'rain-man';  
    setTimeout(  
        function(){ alert(str); } //这是一个匿名函数  
    , 2000);  
}  
checkClosure();
```
> 这个例子看上去十分的简单，仔细分析下它的执行过程还是有许多知识点的：  
> 1. checkClosure函数的执行是瞬间的（也许用时只是0.00001毫秒），  
> 2. 在checkClosure的函数体内创建了一个变量str，在checkClosure执行完毕之后str并没有被释放  
> 3. 这是因为setTimeout内的匿名函数存在这对str的引用。待到2秒后函数体内的匿名函数被执行完毕,str才被释放。
> 

```javascript
var outer = null;  
     
(function(){  
    var one = 1;  
    function inner (){  
        one += 1;  
        alert(one);  
    }  
    outer = inner;  
})();  
     
outer();    //2  
outer();    //3  
outer();    //4
```

> **分析**:   
> 1. 这段代码中的变量one是一个局部变量（因为它被定义在一个函数之内），因此外部是不可以访问的。
> 2. 但是这里我们创建了inner函数，inner函数是可以访问变量one的；  
> 3. 又将全局变量outer引用了inner，所以三次调用outer会弹出递增的结果。 
> **很重要的一点:outer变量只是引用了函数**


-----
# 闭包在for循环中的应用

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
----
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
你会发现当鼠标移过每一个\<li>元素时，总是弹出4，而不是我们期待的元素下标。这是为什么呢？  
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
使用闭包十分容易造成浏览器的内存泄露，严重情况下会是浏览器挂死





