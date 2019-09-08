---
title: node里的模块化commonJS
date: 2019-08-15 16:40:29
tags: [JavaScript, Node]
---

## 功能

模块化编程, 避免命名冲突, 全局变量污染

## 使用: 导入导出

### 导出对象时, 比如导出 {a:1}

  `module.exports = {a: 1}`
  或者
  `exports.a = 1`**注: 不能 exports = {a: 1}**

### 导出函数或数组时就不能用exports了, 比如导出 [1,2,3]

  `module.exports = [1,2,3]`

## 实现(原理)

node解析时:

1. 闭包实现隔离变量: 包了一层闭包
2. 导入导出: 包了一层处理

## 源码

```js
// 准备module对象:
var module = {
    id: 'hello',
    exports: {}
};
var load = function (exports, module) {
    // 读取的hello.js代码:
    function greet(name) {
        console.log('Hello, ' + name + '!');
    }
    module.exports = greet;
    // hello.js代码结束
    return module.exports;
};
var exported = load(module.exports, module);
// 保存module:
save(module, exported);
```

## 总结

1. 模块可以不引入直接使用module/exports就是因为是他是外壳提供的参数
2. 这样, 所有的module都被save了, require的时候根据名字找到,然后把里边的module的exports返回就可以使用了
3. 可以看出来, 导出时不能直接`exports = {xxx: xxx}`, 只能 `exports.xxx = xxx`, 原因是: 重新赋值相当于把形参和对象脱离引用, module.exports并不会随之改版, 改key时 由于引用的同一个地址, 所以修改会同步, 是可以生效的
