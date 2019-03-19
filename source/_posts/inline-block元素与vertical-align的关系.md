---
title: inline-block元素与vertical-align的关系
date: 2017-01-06 23:23:32
tags: [CSS,前端]
---

[*参考博客地址 (in CSDN)*](http://blog.csdn.net/wmaoshu/article/details/52964251)

对于inline-block元素来说，他的基线取决于元素本身的特性，在该元素中没有行内子元素（注意是DOM树的子元素，有块子元素不算或者块子元素中有行元素这个行元素算）的时候或者overflow不为visible，该inline-block的基线为margin-bottom的下边界。否则，以该元素中最后一个行框子元素的基线为该元素的基线。