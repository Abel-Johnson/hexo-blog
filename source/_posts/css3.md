---
title: CSS3
date: 2017-01-06 23:23:32
tags: [CSS3, 前端]
---

## 盒倒影: 

```css
-webkit-box-reflect: below 0px -webkit-linear-gradient(rgba(0,0,0,0) 60%, rgba(0,0,0,1) 100%);
```

###### box-reflect 倒影 参数
*   方向     above|below|left|right;  
* 距离  
* 		渐变（可选） -webkit-linear-gradient(rgba(0,0,0,0) 60%, rgba(0,0,0,1) 100%)  


## css3新增选择器

### 属性选择器
1. E[attr]只使用属性名，但没有确定任何属性值
2. E[attr="value"]指定属性名，并指定了该属性的属性值
3. E[attr~="value"]指定属性名，并且具有属性值，此属性值是一个词列表，并且以空格隔开，其中词列表中包含了一个value词，而且等号前面的“〜”不能不写
4. E[attr^="value"]指定了属性名，并且有属性值，属性值是以value开头的
5. E[attr$="value"]指定了属性名，并且有属性值，而且属性值是以value结束的
6. E[attr*="value"]指定了属性名，并且有属性值，而且属值中包含了value
7. E[attr|="value"]指定了属性名，并且属性值是value或者以“value-”开头的值（比如说zh-cn）
