---
title: SVG
date: 2017-01-06 23:23:32
tags: [ 前端]
---
是基于xml的技术
xml可以自定义标签,html的标签是固定的

```xml
<?xml version='1.0' encoding="utf-8">
<!DOCTYPE svg .....>
<svg xmlns="http://www.w3.org/2000/svg">
```
把svg(xml文件)引入html的方法:

```html
1. <img src='1svg.svg(xml其实不管后缀名的)'/>
2. <div style="background:url(1svg.svg)">
3. <iframe src='1svg.svg'></iframe>
```
---

在html5中:
可以在html中直接写

```html
<svg id="svg1" xmlns="http://www.w3.org/2000/svg" width="100%" height="100%">
	<!--底下所有坐标都是相对于svg标签的父级的坐标-->
	<!--画圆-->
	<circle 
		cx="圆心坐标x(不带单位)" 
		cy="圆心坐标y(不带单位)" 
		r="半径(不带单位)" 
		
		//也可以把下面样式键值对写到style属性里
		fill="填充颜色" 
		stroke="边框颜色"
		stroke-width="边框宽度(不带单位)"
	>
	</circle>
	<!--画矩形-->
	<rect 
		width="200" 
		height="200" 
		x="100(左上角坐标)" 
		y="100" 
		fill="填充颜色" 
		rx="圆角半径x" ry="圆角半径y"(也可以只写一个)
	></rect>
	<!--划线-->
	<line
		x1="" y1="" x2="" y2=""//两个端点的坐标
		stroke="线条颜色"
		stroke-width="线条粗细"
		stroke-opacity=".5(线条透明度)"
	></line>
	<!--g标签:是一个容器标签,用来包裹一组元素,可以对子元素进行统一操作-->
	<g stroke-width这些共用属性可以设置然后子类自动继承 也可以用transform:translate(0,0)达到整体平移的效果>
		<circle></circle>
		...
	</g>
	<!--文字-->
	<text 
		x="文字基点位置坐标x" y="文字位置坐标y" 
		font-size="" 
		text-anchor="middle(左右居中(关于前面设置坐标基点对称))/start/end"
	>文字</text>
	<!--图片-->
	<image
		x="左上角坐标x"
		y="左上角坐标y"
		width=""
		height=""
		xlink:href="img/main.png(图片url)"
	></image>
	<!--折线-->
	<polyline
		points="50 50 200 300 230 230(每个点的坐标)" (可以用空格隔开,也可以用逗号隔开)
		stroke="black" 
		stroke-width="4" 
		fill="none"(不设置会默认用黑色填充)
	></polyline>
	<!--多边形(与折线区别就是他是闭合的)-->
	<polygon></polygon>
	<!--path路径-->
	<path
		d="M50 100L200 200L20 30H200V100Z"
		//M是绘制起点
		//L是途经点
		//Z是首尾闭合(要写在最后)
		//H是水平绘制
		//V是垂直绘制
		//A绘制弧线
			- X半径长度
			- Y半径长度
			- 角度(针对椭圆,正圆直接写0就好)
			- 弧长: 0 小弧 1 大弧
			- 方向: 0 逆时针 1 顺时针
			- 终点X坐标
			- 终点Y坐标
		//字母区分大小写,小写意味着是长度(相对坐标),大写是坐标(绝对坐标)
	></path>
	
<svg>

---
<script>
使用js动态生成

1. 生成标签
var oSvg = document.createElementNS(
	两个参数: 
		1. "命名空间"
		2. (标签名)"svg"/"circle"/"line"...
)
2. 设置属性
oSvg.setAttribute("xmlns","命名空间")
oSvg.setAttribute("width","100%")
oSvg.setAttribute("height","100%")
3. append到容器div里
oParent.appendChild(oSvg)

</script>

3. 运动
<animate attributeName="width" dur="1" from="50" to="100">宽度1s从50到100