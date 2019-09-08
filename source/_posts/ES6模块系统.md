---
title: ES6模块系统
date: 2019-05-08 16:06:10
tags: [JavaScript,  前端, 模块化]
---

1. export关键字导出对象
2. import关键字导入模块
3. 支持模块异步加载


> es6中,每个js文件都是一个模块,有自己的私有作用域,好处就是对实现细节进行了封装,只会导出必要的功能
> 

```
// 第一组
export default function crc32() { // 输出
  // ...
}

import crc32 from 'crc32'; // 输入
```

```js
// 第二组
export function crc32() { // 输出
  // ...
};

import {crc32} from 'crc32'; // 输入
```

***可以看出，使用export default时，import语句不用使用大括号***。

import和export命令只能在模块的顶层，不能在代码块之中。否则会语法报错。

这样的设计，可以提高编译器效率，但是没有办法实现运行时加载。

**因为require是运行时加载，所以import命令没有办法代替require的动态加载功能。所以引入了import()函数。完成动态加载。**

import(specifier)
specifier用来指定所要加载的模块的位置。import能接受什么参数，import()可以接受同样的参数。

import()返回一个Promise对象。

```js
const main = document.querySelector('main');

import(`./section-modules/${someVariable}.js`)
  .then(module => {
    module.loadPageInto(main);
  })
  .catch(err => {
    main.textContent = err.message;
  });
```

## 导出对象

#### 分类

1. 命名式导出（名称导出）*每个模块可以多个*

    - export { name1, name2, …, nameN };
    - export { variable1 as name1, variable2 as name2, …, nameN };
    - export let name1, name2, …, nameN; // also var, function, class
    - export let name1 = …, name2 = …, …, nameN; // also var, const

2. 默认导出（定义式导出）*每个模块仅一个*

    - export default expression;
    - export default { name1, name2, …, nameN };
    - export default function (…) { … } // also class, function*
    - export default function name1(…) { … } // also class, function*
    - export { name1 as default, … };

* 高级的,模块继承 *继承模块并导出继承模块所有的方法和属性 as－重命名导出“标识符” from－从已经存在的模块、脚本文件…导出*

    - export * from …;
    - export { name1, name2, …, nameN } from …;
    - export { import1 as name1, import2 as name2, …, nameN } from …;

#### 错误

1. export在导出接口的时候，必须与模块内部的变量具有一一对应的关系

```js
export 1; // 绝对不可以
 
var a = 100;
export a;
```
大部分风格都建议，模块中最好在末尾用一个export导出所有的接口，例如：

```js
export {fun as default,a,b,c};
```

## 导入模块

- import defaultMember from "module-name";
- import * as name from "module-name";
- import { member } from "module-name";
- import { member as alias } from "module-name";
- import { member1 , member2 } from "module-name";
- import { member1 , member2 as alias2 , [...] } from "module-name";
- import defaultMember, { member [ , [...] ] } from "module-name";
- import defaultMember, * as name from "module-name";
- import "module-name";


```js
import a from './d';

// 等效于，或者说就是下面这种写法的简写，是同一个意思

import {default as a} from './d';
```
> 1. 简单的说，如果import的时候，你发现某个变量没有花括号括起来（没有*号），那么你在脑海中应该把它还原成有花括号的as语法。
> 2. as重命名一般用来解决接口重名的问题


-------

