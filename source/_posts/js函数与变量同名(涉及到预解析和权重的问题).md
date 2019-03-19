---
title: js函数与变量同名(涉及到预解析和权重的问题)
date: 2017-01-06 23:23:32
tags: [DOM,HTML,JavaScript,  前端]
---

```javascript
console.log(a);
var a = 3;
function a(){}
输出的结果是：[Function: a]
```

注意一下几点就能知道原因了！

1. 函数声明会置顶
2. 变量声明也会置顶
3. **函数声明比变量声明更置顶：）**
4. 变量和赋值语句一起书写，在js引擎解析时，会将其拆成声明和赋值2部分，声明置顶，赋值保留在原来位置
5. **声明过的变量不会重复声明**

按以上的规则代码等价为

```javascript
function a(){}
var a;//实际无效
console.log(a);
a = 3;
```