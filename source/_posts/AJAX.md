---
title: AJAX
date: 2017-01-06 23:23:32
tags: [DOM,HTML,JavaScript,  前端]
---

***作用: 从服务端获取数据***
---

1. 浏览网页流程  (打开页面.请求服务端内容)  
客户端(client)向服务器(server)请求数据
服务器返回数据给客户端
2. 协议:
	* file:// (本地协议, 访问本地的数据)
	* https:// (超文本传输协议,访问到服务器的数据)
3. 域名:
	baidu.com
4. 如果想让别人访问自己的页面,就要把自己的资源上传到别人可以访问到的服务器上
5. wamp(win平台服务器集成软件)
6. 域名最终会解析成ip,如www.baidu.com会被解析成ip: 192.202.x.x,所以直接在地址栏输入ip也可以访问
7. 自带计算机域名是localhost,ip为127.0.0.1
8. jQuery里的格式:

	```javascript
	$.ajax({
		url: 'xxx.php',
		data: {
			key1: value1,
			key2: value2,
			....			
		}
		success: function(data){
			data = JSON.parse(data);
			...(渲染语句)
		},
		error: function(data,a) {
			console.log(data)----data是一个ajax对象,其中有属性status(表征获取成功与否的http状态码,如404)和描述statusText	
			console.log(a)---(如果是error就说明出错了)	
		}
	})
	```
9. 一些状态码:
	* 200请求成功
	* 304成功,(特殊的,走的缓存)
		


> **关于apache配置:**
> 
> 1. 有冲突,改端口
> 2. 把有个地方的配置改成all
> 3. 关于修改根目录,详见同级文件夹里的apache说明txt文件

---

## AJAX(Asynchronous Javascript And Xml)工作原理

校验注册信息,如用户名 时,先用正则表达式判断语法,然后发给后台,校验是否已存在

<form action="xx(提交地址).xx" method="(提交方法)get/post">
两种方法中只有**get**方式才会把所填的信息**显示在地址栏**上,post不会显示在地址栏

search/hash
search是?开头的键值对: ?key=value & key=value

ajax对象: XMLHttpRequest构造函数xhr

---

#### *get和post*区别:  
一个是通过地址栏url传递数据  
另一个是通过把数据用send方法传给后台,数据不会暴露在地址栏


---

原生方法:

1. 构造对象  
var xhr = new XMLHttpRequest;

2. 链接地址,准备数据  
xhr.open('Get','http://xxx.php?user=' +username,false) 
	xhr.open('Post','http://xxx.php?user=' ,true)  
	*上边第三个参数是是否异步,false是同步,true是异步*

3. 服务器响应请求,所有请求响应完毕后会触发一个事件  (该事件要提前绑定好,有可能响应速度很快)

	```javascript
	xhr.onload = function() {
		//校验完成(无论成功还是失败)后返回的数据(如'用户名已经存在'):
		var data = xhr.responseText;
		//然后使用该属性,显示在页面中就好,就做到了局部刷新
		
		//获取到状态码,
		xhr.status
		//....此时,校验已完成....
	}
	
	```

4. 发送给服务器  
**GET方法下**:  
`xhr.send()`   
**POST方法下**:  
post可以提交多种格式的数据,设置一个请求头,告诉后端发送的是什么样的格式的数据   
				`xhr.setRequestHeader("Content-Type",'application/x-www-form-urlencoded') `  
最后,  
`xhr.send("user=" + this.value)`

---
#### 流程如下   
每个流程对应一个**编号**,一个属性的值(xhr.readyState)

||步骤|编号(xhr.readyState)|
|:---:|:---|:---:|
|1|构造对象|0|
|2|链接地址,准备数据 |1|
|3|发送给服务器 |2|
|4|服务器响应,返回数据|3|
|5|全部响应完毕,触发onload事件|4|

---
> **异步&同步**  
> js代码是*自上而下*执行的(也即默认同步执行)   
> 异步就是说前面代码很耗时时,下面代码不会等到上面代码执行完后就先执行  
> 同步就是严格自上而下执行



#### 自己封装的ajax函数(仿jquery中的`$.ajax()`)

```html
<!DOCTYPE html>
<html lang="zh-cn">
	<head>
		<meta charset="utf-8" />
      	<title></title>
		<script src="./jq3.js"></script>
	</head>

	<body>
		<p>用户名：<input id="userName" type="" name="user"><span id="tip"></span></p>
		<p>手机号码：<input id="tellNumber" type="" name="user"><span id="tip1"></span></p>
		<p>密码：<input type="password" name=""></p>
		<input type="button" name="" value="提交" />

		<script src="./jq3.js"></script>
		<script>
			/*
				封装的是get请求和post请求这两种情况
				
				不一样的：
					请求方式不一样 传参  默认 get
					请求地址不一样 传参  *
					可设置同步异步  传参  默认 true
					请求的数据      传参  默认为""
					成功的回调函数  传参  
						执行成功回调函数的时候得到后端传过来的数据
					失败的回调函数  传参  
						状态码

			*/
			function ajax(options){
				var defaults = {
					url:"",        //请求地址
					method:"get",  //请求方式
					data:'',       //请求的数据
					isAsyn:true,   //是否为异步
					success:function(){}, //成功执行的回掉函数
					error:function(){}    //失败执行的回掉函数
				};

				$.extend(defaults,options);
				//如果地址没有填写，抛出错误
				if( defaults.url.trim() === "" ){
					throw new Error("请求地址不能为空");
				}

				var xhr = new XMLHttpRequest();

				if( defaults.method === "get" ){
					defaults.url += "?"+defaults.data;
				}

				xhr.open(defaults.method,defaults.url,defaults.isAsyn);


				xhr.onload = function (){
					if( xhr.status === 200 ){  //成功
						defaults.success(xhr.responseText)
					} else { //失败
						defaults.error(xhr.status,xhr.statusText);
					}
				};


				if( defaults.method === "get" ){
					xhr.send();
				}else if(defaults.method === "post"){
					xhr.setRequestHeader("Content-Type",'application/x-www-form-urlencoded');
					xhr.send(defaults.data);
				}
			}

			userName.onblur = function (){
				ajax({
					url:'http://localhost/2017-01-10/php/post.php',
					data:"user="+this.value,
					method:"post",
					success:function (data){
						console.log( data );
					}
				});	
			};

			tellNumber.onblur = function (){
				ajax({
					url:"http://localhost/2017-01-10/php/get.php",
					data:"user="+this.value,
					success(data){
						console.log(data);
					}
				});		
			};

		</script>
	</body>
</html>
```
---

#兼容性 
get方法:IE下,中文要手动进行url编码,ie才会识别  
post方法: 由于设置了请求头,就不需要url编码了
>  url编码  
> encodeURI("哈")-----编码  
> decodeURI("%E5%F2%DD")-----解码


#上传&&下载

下载会调用第三方下载工具,就不说了  
主要讲**上传**

**上传本地文件(用input:files标签)**  

```html
<input type="file" name="(后端约定好的)" id="fileInput">
<input type="button" value="上传" id="btn">
<script>
	//要用post方法  
	btn.onclick = function() {
		var xhr = new XMLHttpRequest;
		xhr.open("post", url, true);
		//不能用value,value只会拿到该文件的本地路径,要用files,对应的是一个数组
		console.log(fileInput.files[0]);
		//用files拿到要上传的文件以后,要把它转换成二进制文件↘
		var fd = new FormData();
		
		//append()是fd对象的方法,第一个参数是key值(前后端约定好的) 
		fd.append("files", fileInput.files[0]);
		
		
		//就不用这么写了---xhr.send("file="+fileInput.files[0]);
	}
</script>
```

----

# 跨域
1. 同源策略(域名,协议,端口()http:80;https:443;ftp:21 22 23相同)  
	百度只能获取到自己的数据,但不能访问到taobao的数据
> 	www.baidu.com 主域名(一级域名)  
> 	news.baidu.com 二级域名  
> 	123.ss.baidu.com 三级域名
	
	当浏览器的百度tab页执行一个脚本的时候会检查这个脚本是属于哪个页面的，  
即检查是否同源，只有和百度同源的脚本才会被执行

2. 电脑上每个软件都有一个端口


# 跨域方式:
1. Ajax方法
	1. 后端加一个请求头部,允许指定ip或者*(任何ip)访问数据
	2. 请求本地服务器的后台程序,在这个后台程序里请求别的服务器的后台数据(后台之间请求数据不存在跨域问题)
	3. ~~flash方法~~
2. jsonp(json+padding用json格式来填充数据)方式  
	后端设置jsonp的请求格式 ,只能用get方式   
	`<img src可以用base64(是一串字符串,图片的一种格式)来写,浏览器不关心里边数据是什么样子,他关心数据能否转成一张图片>`,`<script>`,`<link>`三类标签是可以跨域的  
	jsonp是用script标签来跨域的,举例:
	1. (动态引入js)点击document生成一个script标签,这个标签的src属性是外部的php,js之类的文件的地址,这个时候在他们的文件内部要有一个函数调用,如fn();与此同时在函数外部,要定义这个函数,以便执行刚才的调用
	
		```javascript
		function abc(data) {
			console.log(data);
		}
		document.onclick = function() {
			var script = document.createElement("script");
			script.src = "http://192.168.2.138/2017-11-12/kuayu/jsonp.php?cb=abc"
			document.body.appendChild(script);
		}
	```
	`cb => callback`
	