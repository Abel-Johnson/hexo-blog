---
title: Linux和node-sass
date: 2019-07-02 19:51:21
tags: [Linux]
---

## Node Sass could not find a binding for your current environment: Linux 64-bit with Node.js 6.x

> 在linux上启动程序遇到了下面的问题：
> ERROR in Missing binding /home/linux-haow/文档> /seeker/client/node_modules/node-sass/vendor/linux-x64-48/binding.node
> [1] Node Sass could not find a binding for your current environment: Linux 64-bit with > Node.js 6.x
> [1] 
> [1] Found bindings for the following environments:
> [1]   - Linux 64-bit with Node.js 4.x
> [1] 
> [1] This usually happens because your environment has changed since running `npm install`.
> [1] Run `npm rebuild node-sass` to build the binding for your current environment.



解决办法：

```
At first going into project folder. Then write bellow code
npm rebuild node-sass --> Enter
```

--------------------- 
作者：csdn_haow 
来源：CSDN 
原文：https://blog.csdn.net/csdn_haow/article/details/53501988 
版权声明：本文为博主原创文章，转载请附上博文链接！