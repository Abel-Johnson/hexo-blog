---
title: 对于table元素的DOM操作
date: 2017-01-06 23:23:32
tags: [DOM,HTML,JavaScript,前端]
---

1. table.tHead---获取表格头部(单数，获取到的是一个元素)
2. table.tFoot---获取表格底部(单数，获取到的是一个元素)
3. table.tBodies---获取表格主体(复数,获取到的是一个集合)
4. 	tBodies[n].rows/tHead.rows/tFoot.rows(复数,获取到的是一个集合,就是表格的行tr)
5. rows[n].cells(复数,获取到的是一个集合,就是表格的td)