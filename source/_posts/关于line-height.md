---
title: 关于line-height
date: 2017-01-06 23:23:32
tags: [CSS,  前端]
---
[*参考博客地址 (in CSDN)*](http://blog.csdn.net/wmaoshu/article/details/52961394)
http://blog.csdn.net/wmaoshu/article/details/52961394
[还有简书上的博客](http://www.jianshu.com/p/f1019737e155)http://www.jianshu.com/p/f1019737e155


line-height = 行内元素的内容区大小 + 上下行间距（或者可以称作半行间距)

## 行内元素里的一些东西：

行内布局是一个很大的坑，今天总结了一下行内元素框模型，有下面几个方面：  
 
- 匿名文本：未被包含在行内元素的字符串

- em框：em框在字体中定义，也被称为字符框

- 内容区：内容区可以是元素中各个字符的em框串在一起构成的框，也可以由元素中字符字形描述的框。

- 行间距：font-size与line-height之差 就是行间距

- 行内框：通过向内容区增加行间距来描述，对于非替换元素行内框刚好等于line-height

- 行框：这是包含该行中出现的行内框的最高点和最低点的最小框，意思是行框上边界要高于最高行内框，最低也要大于最低的行内框