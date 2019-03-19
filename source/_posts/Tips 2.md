---
title: Tips2
date: 2017-01-06 23:23:32
tags: [ 前端]
---
1. 如果既想外联又想把js写在body的最后面,就这么写: 

	```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
		<link rel="stylesheet" href="xx.css">
	</head>
	<body>
		....
		<script src="xxx.js"></script>
	</body>
</html>
```

2. `getElementById("xx")`,找不到会返回null
3. 变量命名规范:
 * 组成:数字,字母,下划线,$
 * 开头:非数字
 * 不许使用关键字,保留字
4. 内层循环的语句想直接跳出外层循环

	```javascript
	a:for(var i = 0;i<2;i++){//为外层的for取个名字a
		for(var j = 0;j<3;j++){
			if(j == 1){
				break a;//跳出的是名为a的 for循环，for循环内部后面的代码都不执行了
			}
			alert("i是："+i+",j是："+j);
		}
	}
	```

