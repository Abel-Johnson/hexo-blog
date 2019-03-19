---
layout:
title: 你不知道的hexo
date: 2019-03-13 16:10:25
tags: [Hexo]
---

## Hexo添加Toc支持，生成文章目录

> 2015年05月14日
> Hexo提供了诸多插件来增强博客体验，地址http://hexo.io/plugins/。
> 在博客搬迁的时发现一个生成文章目录的插件，hexo-toc。
> 
> hexo-toc
> 为防插件误认标记，文章以下出现的 ttoc 实际为 toc。

**使用方法跟显示文章摘要类似，在Markdown中需要显示文章目录的地方添加 <!-- ttoc -->。**

1. 安装
`npm install hexo-toc --save`
2. 配置
在博客根目录下的 `_config.yml` 中如下配置：

```json
toc:
  maxDepth: 3
```

`maxDepth 表示目录深度为3，即最多生成三级目录。`

3. 好了，现在重启Hexo预览下效果吧。