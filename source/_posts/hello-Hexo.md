---
title: Hello Hexo
tags: [Hexo]
---
Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

<!-- toc -->

## Quick Start

### Create a new post

``` bash
$ hexo new "My New Post"
```

More info: [Writing](https://hexo.io/docs/writing.html)

### Run server

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

### Generate static files

``` bash
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

### Deploy to remote sites

``` bash
$ hexo deploy
```

More info: [Deployment](https://hexo.io/docs/deployment.html)

## 清除hexo缓存

``` bash
$ hexo clean
```

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