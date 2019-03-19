---
title: gitignore添加后无效的解决办法
date: 2018-02-07 15:30:31
tags: [Git]
---

> 在工程中很容易出现.gitignore并没有忽略掉我们已经添加的文件，那是因为.gitignore对已经追踪(track)的文件是无效的，需要清除缓存，清除缓存后文件将以未追踪的形式出现，这时重新添加(add)并提交(commit)就可以了。

```bash
// 不要忘了后面的 . 
git rm -r --cached .
git add .
git commit -m "comment"
```