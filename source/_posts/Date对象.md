---
title: Date对象
date: 2017-01-06 23:23:32
tags: [JavaScript,  前端]
---

 *通过new Date()可以生成一个对象,该对象包含当前系统时间的所有信息*
 
1. 	var t1 = new Date(2016,9,27,10,5,12);//年，月，日，时，分，秒(注意，月份是从0开始的)
2. var t2 = new Date('Oct 27,2016 10:10:12')//传入一个字符串 "月份  日期,年份 时:分:秒"
3. var t3 = new Date(new Date().getTime())//传入一个时间戳


*这些方法都会返回一个数字*

* 	getFullYear()  	    获取年
* 	getMonth()     	    获取月  (月份从0开始计算)
* 	getDate()     	    获取日 

	```javascript
	t.setDate(0);//设置日期为0
	t.setDate(32);//不会报错，会自动向下一个月进位
	```
*   getDay()     	    获取星期 （星期从周日开始，从0开始计算）
* 	getHours()   	    获取小时
* 	getMinutes()  	    获取分钟
* 	getSeconds()  	    获取秒钟
* 	getMilliseconds() 获取毫秒
* 	getTime()      	    获取1970-01-01 00:00:00到目前日期对象的时间差（单位:毫秒）

设置日期对象的方法setXxx()返回的是时间戳