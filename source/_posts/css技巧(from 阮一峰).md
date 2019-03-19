---
title: CSS的小技巧
date: 2017-01-06 23:23:32
tags: [DOM,HTML,JavaScript,  前端]
---

## 3D按钮
要使按钮具有3D效果，只要将它的左上部边框设为浅色，右下部边框设为深色即可。 

```css
　div#button {
　　　background: #888;
　　　border: 1px solid;
　　　border-color: #999 #777 #777 #999;
　}  
```
　　
## font属性的快捷写法
font快捷写法的格式为：

```css
　　body {
　　　　font: font-style font-variant font-weight font-size line-height font-family; 
　　}
```

##  获得焦点的表单元素
当一个表单元素获得焦点时，可以将其突出显示：

```css
	input:focus { border: 2px solid green; }
```

## CSS提示框
*当鼠标移动到链接上方，会自动出现一个提示框。*

```css
　　<a class="tooltip" href="#">链接文字 <span>提示文字</span></a>
```
CSS这样写：

```css
　　a.tooltip {position: relative} 
　　a.tooltip span {display:none; padding:5px; width:200px;} 
　　a:hover {background:#fff;} /*background-color is a must for IE6*/ 
　　a.tooltip:hover span{display:inline; position:absolute;}
```


## 固定位置的页首
*当页面滚动时，有时需要页首在位置固定不变，CSS语句可以这样写，效果参见http://limpid.nl/lab/css/fixed/header*

```css
　　body{ margin:0;padding:100px 0 0 0;}
　　div#header{
　　　　position:absolute;
　　　　top:0;
　　　　left:0;
　　　　width:100%;
　　　　height:<length>;
　　}
　　@media screen{
　　　　body>div#header{position: fixed;}
　　}
　　* html body{overflow:hidden;}
　　* html div#content{height:100%;overflow:auto;}
```

## 容器的水平和垂直居中
HTML代码如下：

```html
　　<figure class='logo'>
　　　　<span></span>
　　　　<img class='photo'/>
　　</figure>
```
CSS代码如下：

```css
　　.logo {
　　　　display: block;
　　　　text-align: center;
　　　　display: block;
　　　　text-align: center;
　　　　vertical-align: middle;
　　　　border: 4px solid #dddddd;
　　　　padding: 4px;
　　　　height: 74px;
　　　　width: 74px; 
　　　}
　　.logo * {
　　　　display: inline-block;
　　　　height: 100%;
　　　　vertical-align: middle; 
　　　}
　　.logo .photo {
　　　　height: auto;
　　　　width: auto;
　　　　max-width: 100%;
　　　　max-height: 100%; 
　　　}
```

## CSS阴影
外阴影：

```css
　　.shadow {
　　　　-moz-box-shadow: 5px 5px 5px #ccc;
　　　　-webkit-box-shadow: 5px 5px 5px #ccc;
　　　　box-shadow: 5px 5px 5px #ccc;
　　}
```
内阴影：

```css
　　.shadow {
　　　　-moz-box-shadow:inset 0 0 10px #000000;
　　　　-webkit-box-shadow:inset 0 0 10px #000000;
　　　　box-shadow:inset 0 0 10px #000000;
　　}
```