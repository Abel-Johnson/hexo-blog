---
title: 关于 checkBox 与 checked
date: 2017-01-06 23:23:32
tags: [DOM,HTML,JavaScript,  前端]
---
**有两种情况**

1. **PROPS**
	`<input type="checkbox" checked/>`  
	//只要有这个行间属性(不管是什么值),就会是一个框选的效果
	
2. **ATTRIBUTE**
	DOM对象的checked属性:  
	var cb = document.getElementById("cb");  
	`cb.checked` //这个属性表征选框的当前状态,到底有没有选中  
	也可以通过修改这个属性,来达到不点击选框就勾选的效果