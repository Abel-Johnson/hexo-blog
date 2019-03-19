---
title: Cookie&&LocalStorage
date: 2017-01-06 23:23:32
tags: [DOM,HTML,JavaScript,  前端]
---

1. 不同域名下的cookie不能访问
2. 比如登录信息一般会用cookie存起来,访问同一个域名下的不同页面都可以读取到他


## 操作cookie

document.cookie = "key=value"
document.cookie = "key2=value2"
document.cookie = "key3=value3"  
-----设置cookie,这样设置,审查元素的时间特性会是:session(会话阶段),浏览器关闭后,cookie会自动清空  

这个时间(想要让cookie在未来某个时刻清空)也可以设置(例如一些网站会有记录登录状态7天的选项)

```javascript
var d = new Date();
d.setDate(d.getData()+7);//设置7天后的时间

/*d.toUTCString()把一个日期对象转换成字符串,采用世界时间UTC格式*/

document.cookie = "key3=value3; expires(到期)="+d;  
```
---

document.cookie; =>"key=value; key2=value2; key3=value3"  
-----查看cookie

---



-----删除


## 操作localStorage(大部分浏览器支持,最大5M)

```javascript
//具体应用
localStorage.setItem("message",JSON.stringify(message));  
var data = localStorage.getItem('message');
if(data) {
	data = JSON.parse(data);
	....使用...,渲染页面之类的事情
} else {
	//没有从本地存储获取到
	....
}
```

localStorage.setItem(key,value)---设置
localStorage.getItem(key,value)---获取
localStorage.removeItem(key)---清除指定键值对应的本地存储
localStorage.clear()---清除全部本地存储
---storage事件:  
注意的点:  
**在窗口a修改localstorage,只会在b窗口触发storage事件,不会触发自身的storage事件**  
对localStorage进行增删改会触发(多用于不同窗口打开同一页面)

```javascript
window.addEventListener('storage', function() {})
```

