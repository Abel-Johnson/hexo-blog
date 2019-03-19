---
title: css3 渐变( *只能用在背景上* )
date: 2017-01-06 23:23:32
tags: [DOM,HTML,JavaScript,  前端]
---
### 线性渐变
> 格式:  linear-gradient([<起点> || <角度>,]? <点>, <点>…)
			
IE下  `filter:progid:DXImageTransform.Microsoft.gradient(startColorstr='#ffffff',endColorstr='#ff0000',GradientType='1');`

参数  
1. 			起点：从什么方向开始渐变(left、top、left top) -- 默认：top    
2. 			角度：从什么角度开始渐变--xxx deg的形式  
3. 			点：渐变点的颜色和位置--black 50%，位置可选

代码示例

```css
background-image: linear-gradient(135deg, rgba(255,255,255,0) 0%, rgba(255,255,255,0) 35%, rgba(255,0,0,0.6) 50%, rgba(255,255,255,0) 65%, rgba(255,255,255,0) 100%);
```

### 镜像渐变
> 格式:  radial-gradient([<>]? [<形状> || <大小>,]? <点>, <点>…);

参数  	
						
1. 	起点：可以是关键字（left,top,right,bottom），具体数值或百分比
2. 形状： ellipse、circle
2. 	大小 :具体数值或百分比，也可以是关键字（最近端，最近角，最远端，最远角，包含或覆盖 (closest-side, closest-corner, farthest-side, farthest-corner, contain or cover)）;

3. 	注意firefox目前只支持关键字

代码示例

```css
background-image: -webkit-radial-gradient(100px, #fff 0%, red 80%);
```

			