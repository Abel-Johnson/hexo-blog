---
title: DOM操作
date: 2017-01-06 23:23:32
tags: [DOM,JavaScript,  前端]
---

1. parentNode.appendChild(childNode)添加子节点
2. parentNode.insertBefore(childNode1, childNode2),把1插到2之前
	* 第二个参数没有,会报错
	* 第二个参数是null,相当于appendChild
3. parentNode.removeChild(childNode)
4. parentNode.replaceChild(node,childNode)
5. node.cloneNode(boolean)

