---
title: Git-Rebase
date: 2019-03-13 16:14:36
tags: [Git]
---

[rebase](http://gitbook.liuhui998.com/4_2.html)

假设你现在基于远程分支"origin"，创建一个叫"mywork"的分支。

`$ git checkout -b mywork origin`


![](b3b66b1b-cac4-44ab-9d71-26113ef2b9b2.png)

现在我们在这个分支做一些修改，然后生成两个提交(commit).

```bash
$ vi file.txt
$ git commit
$ vi otherfile.txt
$ git commit
...
```

但是与此同时，有些人也在"origin"分支上做了一些修改并且做了提交了. 这就意味着"origin"和"mywork"这两个分支各自"前进"了，它们之间"分叉"了。

![](09cb7f7f-d0b4-4fd0-9191-29325452aaa1.png)

在这里，你可以用"pull"命令把"origin"分支上的修改拉下来并且和你的修改合并； 结果看起来就像一个新的"合并的提交"(merge commit):

![](4dafe2cb-b077-4a92-8422-a5c6331b703e.png)

**但是，如果你想让"mywork"分支历史看起来像没有经过任何合并一样，你也许可以用 git rebase:**

```bash
$ git checkout mywork
$ git rebase origin
```

> 这些命令会把你的"mywork"分支里的每个提交(commit)取消掉，并且把它们临时 保存为补丁(patch)(这些补丁放到".git/rebase"目录中),然后把"mywork"分支更新 到最新的"origin"分支，最后把保存的这些补丁应用到"mywork"分支上。

![](38ce100f-1258-4f0c-ad28-f38dcb5643ed.png)

当'mywork'分支更新之后，它会指向这些新创建的提交(commit),而那些老的提交会被丢弃。 如果运行垃圾收集命令(pruning garbage collection), 这些被丢弃的提交就会删除. （请查看 git gc)

![](f6516cbd-0344-4d9a-95bc-814131f9a3f5.png)

现在我们可以看一下用合并(merge)和用rebase所产生的历史的区别：

![](133a87d1-c7e1-4b41-a9e4-c5ad369a2bc3.png)

在rebase的过程中，也许会出现冲突(conflict). 在这种情况，Git会停止rebase并会让你去解决 冲突；在解决完冲突后，用"git-add"命令去更新这些内容的索引(index), 然后，你无需执行 git-commit,只要执行:

`$ git rebase --continue`
这样git会继续应用(apply)余下的补丁。

在任何时候，你可以用--abort参数来终止rebase的行动，并且"mywork" 分支会回到rebase开始前的状态。

`$ git rebase --abort`





## 合并多个commit

[如果有多次提交,可以用Git命令行合并](https://www.jianshu.com/p/e9b6f92ea07c)

1. 先查看当前的提交和commit ID `git log `
2. 复制要合并前一个的commitID
3. rebase当前的提交`git rebase -i commitID`
4. 然后会进入Vim编辑器,此处应该有vim编辑器速通教程
5. 可以见到 
    - 上方未注释的部分是填写要执行的指令，
    - 而下方注释的部分则是指令的提示说明。
    - 指令部分中由前方的命令名称、commit hash 和 commit message 组成。


    当前我们只要知道 pick 和 squash 这两个命令即可。
    
    - pick 的意思是要会执行这个 commit
    - squash 的意思是这个 commit 会被合并到前一个commit,注意是 前
    一个
    
    我们将 c4e858b5 这个 commit 前方的命令改成 squash 或 s，
    
    然后输入:wq以保存并退出

6. 最后删除多余的commit记录就好了: 
    - 输入dd可以删除一行,
    - 输入 i.o.s.a等切换到编辑模式(底部有个insert标志),修改文字,完成后esc
    - 输入:wq保存即可
7. 重新git log 一下 发现已经修改完毕了

7. 如果这个过程中有操作错误，可以使用 git rebase --abort来撤销修改，回到没有开始操作合并之前的状态。