---
title: 关于jq中事件绑定函数(on,或click之类)的第二个参数selector的用法
date: 2017-01-06 23:23:32
tags: [JQuery,  前端]
---

文档中是这么说的:  
##### `on(events,[selector],[data],fn)`

*selector:一个选择器字符串用于过滤器的触发事件的选择器元素的后代。如果选择的< null或省略，当它到达选定的元素，事件总是触发。*


**所以这个参数应该是一个字符串,而且应该是一个内容是选择器的字符串**

---
示例代码:  

**HTML部分:**

```html
<div id="pg-box">
	<a href="javascript:;" class="home">首页</a>
	<a href="javascript:;" class="prev">上一页</a>
	<span class="pages">
		<a href="javascript:;">1</a>
		<a href="javascript:;">2</a>
		<a href="javascript:;">3</a>
		<a href="javascript:;">4</a>
		<a href="javascript:;">5</a>
	</span>
	<a href="javascript:;" class="next">下一页</a>
	<a href="javascript:;" class="end">尾页</a>
</div>
```


**Javascript部分**

```javascript
$('#pg-box').on('click','a:not(.pages a)',evFn)
```

---
> **分析:**
> 
> 1. 需求:想要给页码1,2,3,4,5加点击事件处理函数 
> 2. 分析:虽然通过jq选择器`$('a').not($(".pages a"))`可以选择到目标元素,但是分析文档,第二个参数要传的是一个**选择器**字符串,例如`'a'`,`'.active'`之类的***选择器+字符串***
> 3. 所以要写成: `'a:not(.pages a)'`就好了