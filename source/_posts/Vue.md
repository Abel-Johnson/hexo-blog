---
title: Vue
date: 2017-01-06 23:23:32
tags: [ 前端]
---
# Vue(全自动版模板引擎)
*双向绑定: 修改数据,自动同步修改DOM*  
*Vue是在浏览器解析DOM后才获取到元素进行修改的*
使用: 

```javascript
**View部分**
//关于'指令':写在html元素上的自定义属性,以'v-'开头
<div id='box' v-if="onoff"(里边onoff是true的时候就增加上,是false就删掉(增删DOM元素)) v-show="onoff"(里边onoff是true的时候就显示,是false就隐藏(修改css的display))>
	<div>{{message}}</div>
	<div>{{c_mes}}</div>
	<li v-for="val(,ind如果用得到索引值的时候) of(或in) list">{{val.title}}</li>
	<input id="addInfo" v-on:keyup="addInfo"(可以简写为@keyup.13
		//.13是事件修饰符(代表keyCode),  
		//还有.enter之类的,相当于直接把过滤规则写在这里,就不需要写在函数里了*/) 
		//v-model="value" 
		//v-bind(可以简写为:,使用数据动态绑定给自定义属性):abc="mes"//class="{class名:布尔值}"表示通过对应的布尔值确定要不要加class"
	/>
	//在绑定事件处理函数的时候有两种方法:    		//@click="fn"//@click="fn()"    
	//第一种方法调用的时候第一个参数就是事件对象,
	//第二个方法第二个参数是事件对象
	<p>同步表单value值改变: {{value}}</p>
</div>

`v-show不支持<template>`
				<template v-if....>
					<li></li>
				</template>


<p v-if="">条件一满足</p>
<p v-else-if="">条件二满足</p>
<p v-else="">所有条件都不满足</p>
!!!注意,这种用法必须紧挨着

**Module部分**
var dataArr = [
	{
		title: xx,
		id: 0
	},
	{
		title: xx,
		id: 0
	},
	...
]


**VM部分**
//根实例,传入一个选项参数
new Vue({
	el(挂载点: 值可以是选择器字符串,也可以是DOM对象):'#box'//此时id为box的标签下的所有元素,都归vue管理
	data(这里的属性都是挂载在根实例上的): {
		message: "Hello Vue",
		list: dataArr,
		value: ""//这个值会实时同步此刻的value值,
		mes: "xx",
		onoff: true
	}
	
	methods: {//放置所有函数,包括事件处理函数
		addInfo: function(){
			//里边的this指向Vue对象(根实例),所以通过this.list就可以找到数据(Module),然后用push之类的方法就可以更新数据
		};
		xxx(如双击事件): function() {
			想获取到渲染后的DOM结构的某个元素 并操作,  
			 步骤如下: 
			 1. 给要获取的元素html标签里加一个自定义属性ref  :ref="xxx";
			 2. 然后要获取到修改数据同步渲染完结构后的那一刻触发事件的事件处理函数this.$nextTick(function(){//这里写这一刻要做的事,!!一定要写!!})
			 3. 然后在函数体里,获取到vue对象上的一个自带属性vm(或者this也可以).$refs,对应的值是一个对象,里边放着所有有ref属性的html元素的DOM对象,然后通过ref对应的属性值['ref属性值xxx'],找到这些特定元素的小集合,然后通过[0(下标)]就可以取出来进行操作
		}
	}
	
	computed(计算后属性): {
		c_mes: function() {
			return 数据处理逻辑写在这里
		},
		
		//如果想添加c_mes的读/写方法
		c_mes: {
			get() {
			},
			set(newVal) {
			}
		}
	}
	
	watch(监控器): {
	
		//默认浅度监控
		beWatched: function() {}//只要(data对应的对象里)的(key值对应)的值发生改变,就会触发该函数
		//开启深度监控
		xxx: {
			handler:function(){},
			deep: true 
		}
	}
})
```
! 注意: 行间js\<div onclick = "fn()">而不是="fn"
! 注意: 命名规范,遇到一个,***方法名不能叫delete***


## 三种模板

1. html模板
	
	```html
	<!--视图V-->
	<div v-html='innerHtml'>
	
	<!--数据-->
	var vm = new Vue({
		el:xx,
		data: {
			innerHtml: "<div>xxx</div>"
		}
	})
	```
2. template

	两种方法: 

	1. 字符串模板做选项对象中template的值
	
		```html
		<!--视图V-->
		<div id="box" v-html='innerHtml'><div>
		
		<!--数据m-->
		innerHtml: "<div>xxx</div>"(注意: 只能有一个根节点)
		let obj = {
			...
		}
		
		
		<!--vm-->
		var vm = new Vue({
			el:'#box',
			data: obj,
			template: innerHtml(注意: 会完全替换挂载点元素)
		})
	
		```
	2. script标签存放模板(类似模板引擎)(两点注意: type和id)
	
		```html
		<!--视图V-->
		<div id="box" v-html='innerHtml'><div>
		
		<!--数据m-->
		<script type='x-template' id="temp"><div>xxx</div></script>
		
		let obj = {
			...
		}
		
		
		<!--vm-->
		var vm = new Vue({
			el:'#box',
			data: obj,
			template: '#temp'(注意: 会完全替换挂载点元素)
		})
		```

3. render模板

		编译的时候,编译器会吧模板里的html标签拿出来,都调用一遍render函数

		```html
		<!--视图V-->
		<div id="box" v-html='innerHtml'><div>
		
		<!--数据m-->
		
		
		let obj = {
			...
		}
		
		
		<!--vm-->
		var vm = new Vue({
			el:'#box',
			data: obj,
			render: function(createElement){//这个参数实际上就是一个创建元素的方法
			//语法: createElement(标签名,[数据对象(包括id, class,或自定义属性)],子元素(文本或数组)),这个函数就会返回一个虚拟DOM(VNode)
			
				return createElement(
					"ul"(标签名), 
					
					{  //选项(数据)对象
						class: { //类似v-bind:class={'class': true}
							class名: 布尔值
						},
						style: {
							fontsize: '90px'
						},
						attrs: {
							自定义属性名: "属性值"
						},
						domProps: {
							//DOM对象身上的属性
							innerHTML: '<li>xx</li>'//这条指令的权重较高,底下传的第三个参数就无效了
							value: xxx
							...
						},
						on: {
							绑定事件
						}
						
					},
					
					
					[
						createElement('li',数据1),
						createElement('li',数据2),
						createElement('li',数据3)
					](子元素数组,也可以是单个的就不必要携写成数组了)
					
				)
			}
		})
		```
		
	

## 组件(组合的视图组件)
比如在布局时,可能很多页面都有相同的一块结构,所以把这些具有可复用性的代码块做成组件,比如**element**


每个框架都有自己的实现方法

vue里是通过自定义标签来实现的

步骤: 

1. 注册组件: 全局注册/局部注册	
	1. Vue.component('custom-comp'[自定义标签的名字], 						{
						 template: '只能有一个根元素,传参可以直接用{{props里的属性名}}'[结构字符串]
						 props: ['标签里定义的属性1','属性2'...,(可以直接用vm实例data里的key值)]
						 data: function(){return {里边放自定义的数据}}//要求写成函数是因为如果写成一个对象,那就意味着多个组件共用一个对象,牵一发而动全身,用函数的时候,每次return一个新的对象
						}[选项对象]);
						methods: ...这些都有
						
	2. 特殊的,对于一些受限元素,如ul,table, a之类的只能嵌套特定的元素的标签,浏览器在解析时,会有容错功能,嵌套不合理标签的时候,会把这个标签摘出去到与受限元素平行关系的位置
	3. 既可以注册一个新标签,也可以放在原生html元素上,由于限制嵌套,所以可以用is,代码示例: 

		```html
		<!--方法一-->
		<custom-comp></custom-comp>
		
		<!--方法二-->
		这种方法是不对的,因为浏览器解析时会把里边的不符合规定的元素摘出来
		<ul>
			<custom-comp></custom-comp>
		<ul>
		
		所以要用is
		<ul>
			<li is="custom-comp"></li>
		</ul>
		
		```