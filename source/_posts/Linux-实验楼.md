---
title: Linux-实验楼
date: 2019-03-27 01:15:14
tags: [Linux]
---

## 终端的常用快捷键

![](终端快捷键.png)

## 通配符

![](shell常用通配符.png)

## 文档

你可以使用如下方式来获得某个命令的说明和使用方式的详细介绍：`$ man <command_name>`

通常 man 手册中的内容很多，你可能不太容易找到你想要的结果，不过幸运的是你可以在 man 中使用搜索/<你要搜索的关键字>，查找完毕后你可以使用n键切换到下一个关键字所在处，shift+n为上一个关键字所在处。使用Space（空格键）翻页，Enter（回车键）向下滚动一行，或者使用k,j（vim 编辑器的移动键）进行向前向后滚动一行。按下h键为显示使用帮助（因为 man 使用 less 作为阅读器，实为less工具的帮助），按下q退出。

想要获得更详细的帮助，你还可以使用info命令，不过通常使用man就足够了。如果你知道某个命令的作用，只是想快速查看一些它的某个具体参数的作用，那么你可以使用--help参数，大部分命令都会带有这个参数，如：

`$ ls --help`

## 有趣的命令

### banner 字符画

1. sudo apt-get update
1. sudo apt-get install sysvbanner
1. banner Abel-Johndon
1. banner Abel-Johnson
1. printerbanner -w 50 A


