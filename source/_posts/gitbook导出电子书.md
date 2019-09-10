---
title: gitbook导出电子书
date: 2019-09-10 21:08:25
tags: [电子书]
---

# mac 安装 gitbook

1. 安装 nodejs: `https://nodejs.org/en/`

2. 安装 gitbook-cli：`npm install gitbook-cli -g`

3. Calibre 安装: `https://calibre-ebook.com/download_osx`

4. svgexport 报错解决: `sudo npm install --unsafe-perm -g svgexport`

5. ebook-convert 报错解决 `ln -s /Applications/calibre.app/Contents/MacOS/ebook-convert /usr/local/bin`

6. `git clone 电子书仓库: https://github.com/xxx` => `cd xxx\zh`

7. 指定导出格式
   `gitbook mobi`
   `gitbook pdf`
   `gitbook epub`

8. 生成的电子书在当前文件夹内
