---
title: 关于transition属性
date: 2017-01-06 23:23:32
tags: [CSS,  前端]
---

transition 过渡  
1. transition-property  要运动的样式  （all || [attr] || none）  
	注意：只能过渡样式值为数值的样式  
2. transition-duration 运动时间  s || ms  
3. transition-delay 延迟时间  
	注意：有多个时间值时，第一个时间为过渡时间，第二个时间为延迟时间  
4. transition-timing-function 运动形式   
			
> * 	ease：（逐渐变慢）默认值
> * 	linear：（匀速）
> * 	ease-in：(加速)
> * 	ease-out：（减速）
> * 	ease-in-out：（先加速后减速）
> * 	cubic-bezier 贝塞尔曲线（ x1, y1, x2, y2 ） http://cubic-bezier.com/  
		
				
5.需要分别控制不同的样式时，用逗号分隔，那么每一段被逗号分隔的样式应该是完整的transition的语法
多个不同的样式的执行顺序？
1、同时执行(代码出发时)
2、会根据延迟时间决定谁先谁后(表现时)

代码示例:

```css
transition: 3s 3s width cubic-bezier(.21,1.4,.67,-0.53), 3s height cubic-bezier(1,.18,.8,-0.19),3s background-color cubic-bezier(.18,.83,0,1.51);
```