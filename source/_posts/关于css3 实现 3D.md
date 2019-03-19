---
title: 关于CSS3 实现 3D
date: 2017-01-06 23:23:32
tags: [CSS,前端]
---

> 为一个元素定义perspective（眼睛到屏幕 默认中心 的距离），其后代元素会获得透视效果，只会影响3d变换元素 只设置该属性不设置preserve3d时，静止时是有3d效果的，运动起来就成为了一个平面  
> 
> transform-style：preserve-3d三维效果（默认flat二维效果），只影响其子元素，所有子元素都可以相对父级平面进行变换 只设置该属性不设置preserve3d时，运动起来是有3d效果的，静止时就成为了一个平面

---

比如要写一个3D立体图形,步骤如下:

1. 写两层嵌套:
	
	```html
	<div class="container">
    <div class="piece-box">
        <div class="piece piece-1"></div>
        <div class="piece piece-2"></div>
        <div class="piece piece-3"></div>
        <div class="piece piece-4"></div>
        <div class="piece piece-5"></div>
        <div class="piece piece-6"></div>
   	 </div>
	</div>
	```
2. 给最外层container,加`perspective`  
	给容器(3d图形整体),加 `perspective-origin`/`transform-style: preserve-3d`

	```css
	/*容器*/
	.container {
	    perspective: 1000px;
	 }
	/*piece盒子*/
	.piece-box {
	 	position: relative;//为子元素绝对定位提供参照物 
	 	perspective-origin: 50% 50%;
     	transform-style: preserve-3d;
	 }
	```

2. 由里边那些div,通过`定位和3d变换`拼接好一个完整的3D图形,
3. **容器container**整体做3D变换