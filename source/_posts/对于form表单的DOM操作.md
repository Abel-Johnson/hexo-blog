---
title: 对于form表单的DOM操作
date: 2017-01-06 23:23:32
tags: [DOM,HTML,JavaScript,前端]
---
1. `var form = document.getElementsByTagName("form")[0]`获取到form对象
2. `form.passw`可以直接用formNode.name值 获取到对应的该form下的input/select/textarea元素
3. `form.username.value`可以根据name值直接获取到该form单元的任何子input/select/textarea元素的value值

#### 表单控件的事件:
* form1.userName.onchange//当表单控件的值发生改变的时候触发
* form1.userName.oninput//当输入 表单控件 进行修改内部内容的时候触发，只要输入就会触发
* form1.onsubmit//当表单提交的时候触发
* form1.onreset//当表单重置的时候触发

#### 表单方法
* form1.submit();  
	提交表单到表单对应的action地址
* form1.reset();  
	重置表单到默认状态