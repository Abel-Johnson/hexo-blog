---
title: GIT常用速查
date: 2019-03-13 14:32:33
tags: [Git]
---

<!-- toc -->


## 初始化

1. 配置

    ```bash
    $ git config --global user.name "Your Name"
    $ git config --global user.email "email@example.com"
    ```

2. 替换npm源为淘宝镜像
   
    `npm config set registry https://registry.npm.taobao.org`

    > 检测是否成功
    > // 配置后可通过下面方式来验证是否成功
    > `npm config get registry`
    > // 或
    > `npm info express`

2. 创建仓库

    - if (想建一个空的本地仓库):
      
        ```bash
        // 想建一个空的本地仓库
        $ mkdir learngit
        $ cd learngit
        $ pwd(作用: 显示当前目录)
        /Users/michael/learngit
        // 如果你没有看到.git目录，那是因为这个目录默认是隐藏的，用ls -ah命令就可以看见。
            
        ```
    - else

        ```bash
        git init
        ```
8. 远程仓库
    > 为什么GitHub需要SSH Key呢？因为GitHub需要识别出你推送的提交确实是你推送的，而不是别人冒充的，而Git支持SSH协议，所以，GitHub只要知道了你的公钥，就可以确认只有你自己才能推送。
    > 涉及到push 的权限问题, 公钥不在github仓库配置里就不行

    - 你的本地Git仓库和GitHub仓库之间的传输是通过SSH加密的
        1. 创建SSH Key(除非在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有`id_rsa`和`id_rsa.pub`这两个文件)： `$ ssh-keygen -t rsa -C "youremail@example.com"`
        2. 登陆GitHub，打开“Account settings”，“SSH Keys”页面：，点“Add SSH key”，填上任意Title，在Key文本框里粘贴id_rsa.pub文件的内容

9. 链接远程仓库(先有本地库，后有远程库的时候)
    1. 网站建仓库
    2. `$ git remote add origin git@github.com:michaelliao/learngit.git`
    添加后，远程库的名字就是origin
    `git push -u origin master`
    我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令
    
10. SSH警告
    当你第一次使用Git的clone或者push命令连接GitHub时，会得到一个警告：
    
    > The authenticity of host 'github.com (xx.xx.xx.xx)' can't be established.
    > RSA key fingerprint is xx.xx.xx.xx.xx.
    > Are you sure you want to continue connecting (yes/no)?
    
    这是因为Git使用SSH连接，而SSH连接在第一次验证GitHub服务器的Key时，需要你确认GitHub的Key的指纹信息是否真的来自GitHub的服务器，输入yes回车即可。
    
    Git会输出一个警告，告诉你已经把GitHub的Key添加到本机的一个信任列表里了：
    
    Warning: Permanently added 'github.com' (RSA) to the list of known hosts.
    这个警告只会出现一次，后面的操作就不会有任何警告了。
    
    如果你实在担心有人冒充GitHub服务器，输入yes前可以对照GitHub的RSA Key的指纹信息是否与SSH连接给出的一致。
    
    
## 基本流程

1. it remote add origin 本地库关联远程仓库

    把本地库的内容推送到远程，用git push命令，实际上是把当前分支master推送到远程

    从现在起，只要本地作了提交，就可以通过命令：`$ git push origin master`

    如果要推送其他分支，比如dev，就改成：`$ git push origin dev`

2. 你的小伙伴要在dev分支上开发，就必须创建远程origin的dev分支到本地，于是他用这个命令创建本地dev分支：`$ git checkout -b dev origin/dev`


3. 本地修改后

    `git status => git diff 可以看到底是那些修改`

4. 现在，他就可以在dev上继续修改，然后，时不时地把dev分支push到远程：
    - `$ git commit -m "add /usr/bin/env"`
    - `$ git push origin dev`

12. 冲突
    
    `$ git add readme.txt `
    `$ git commit -m "conflict fixed"`
    `[master cf810e4] conflict fixed`

13. 最后，删除feature1分支：

    `$ git branch -d feature1`


3. LOG

    1. 如果嫌输出信息太多，看得眼花缭乱的，可以试试加上--pretty=oneline参数：
    
        `$ git log --pretty=oneline`

## 基本概念

![](head.png)

commit相当于一个存档,每当你觉得文件修改到一定程度的时候，就可以“保存一个快照”，这个快照在Git中被称为commit。一旦你把文件改乱了，或者误删了文件，还可以从最近的一个commit恢复，然后继续工作，而不是把几个月的工作成果全部丢失。

用HEAD表示当前版本, 上一个`HEAD^`, 上100个`HEAD~100`
本地的push和merge会形成`MERGE-HEAD(FETCH-HEAD)`, `HEAD（PUSH-HEAD)`这样的引用。HEAD代表本地最近成功push后形成的引用。MERGE-HEAD表示成功pull后形成的引用
    
**HEAD指向当前分支, 当前分支指向提交**

## 时光机

1. git revert

    场景: 如果要是 提交了以后，可以使用` git revert`还原已经提交的修改 
	此次操作之前和之后的commit和history都会保留，并且把这次撤销作为一次最新的提交 
	`git revert HEAD` 撤销前一次 commit 
	`git revert HEAD^` 撤销前前一次 commit 
	`git revert commit-id` (撤销指定的版本，撤销也会作为一次提交进行保存） 
	
	git revert是提交一个新的版本，将需要revert的版本的内容再反向修改回去，**版本会递增**，不影响之前提交的内容。
	
2. git reset
    - git log查看提交历史记录
    * HEAD指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令git reset --hard commit_id。
    * 穿梭前，用git log可以查看提交历史，以便确定要回退到哪个版本。
    * 要重返未来，用git reflog查看命令历史，以便确定要回到未来的哪个版本。
    
        > `git reset --hard HEAD^(commitID也可以)`
        > `git push origin xxx --force(同步远程仓库)`

3. [git rebase](http://gitbook.liuhui998.com/4_2.html)

    1. Git删除中间某次commit
    
        1. 首先git log查看提交记录，找到出错的前一笔提交的commit_id
        2. 用命令git rebase -i commit_id ,查找提交记录
        3. 将出错那笔提交的pick改为drop(丢弃)
        4. Esc，:wq
        
        (revert也可以, 但是会留一条记录)

14. 发现拉错分支了, 出现了冲突, 

    1. git reset --hard commitid(本地的push和merge会形成MERGE-HEAD(FETCH-HEAD), HEAD（PUSH-HEAD）这样的引用。HEAD代表本地最近成功push后形成的引用。MERGE-HEAD表示成功pull后形成的引用。)

    2. --hard会冲掉工作区顺带暂存区

1. git只合并某次commit

    > 使用场景：因开发两个分支并行开发，直接合并会造成很多问题。只是想合并某次改变的commit，就可以实用git cherry-pick
    > 通过git log可以查看当前分支的所有提交的哈希值（ID）
    > 切到需要合并的目标分支
    > 运行 git cherry-pick 哈希值
    > 如果遇到error，运行 git status查看 ummerged 下红色的文件路径，用编辑器打开，修改（寻找 === 标志， 上半是旧代码，下半是新代码，自己决定取舍）
    > 完成所有冲突文件修改后，git add 对应文件
    > 运行 git cherry-pick --continue 即完成。
    > 注意：这时会弹出 一段可能会很长的描述性文字，可以全部删除，改成自己要的文字，一般我是保留最上那行，跟之前的历史描述一致，方便查找。不删除也无所谓。然后保存退出即可。
    > 这个界面是vi的编辑界面，一次删除多行的命令为 “数字X+dd” 即可删除当前光标一下的X行内容。


## 错误解决
1. cannot lock ref

    - 错误描述
    
        > git pull的时候
        > error: cannot lock ref 'refs/remotes/origin/dev': ref refs/remotes/origin/dev is at 19070aed6873f8d58f35e4631272b59f13927a1c but expected 8a5b3bda0778070bd6b92123556475c9484e04b8From 120.26.77.241:yourWorkspace/yourProject

    - 解决办法: 

        > 当时是又执行了一遍命令好的
        > 网上推荐的方法:
        > rm .git/refs/remotes/origin/dev
        > 
        > git fetch
2. 无法push

    - [在GitHub多个帐号上添加SSH公钥
](https://www.webmaster.me/uncategorized/add-multiple-ssh-keys-on-github.html)
    - [添加公钥以后, 依然无法push](https://www.jianshu.com/p/be58fa27a704)去到`cd /Users/Johnson/.ssh`然后执行`ssh-add id_rsa2(公钥名)` 就可以了

    - 若执行`ssh-add /path/to/xxx.pem`是出现这个错误:**Could not open a connection to your authentication agent**，则先执行如下命令即可：`ssh-agent bash`

3. 查看git配置

config 配置指令

```bash
git config --global user.name "myname"
git config --global user.email  "test@gmail.com"
```

config 配置有system级别 global（用户级别） 和local（当前仓库）三个 设置先从system-》global-》local  底层配置会覆盖顶层配置 分别使用--system/global/local 可以定位到配置文件

查看系统config`git config --system --list`
查看当前用户（global）配置`git config --global  --list`
查看当前仓库配置信息`git config --local  --list`

## 快捷命令代替手工撤销
1. 批量删除branch中新加的文件(untracked files)

    > 首先确认要删除的文件`git clean -fd -n`
    > 
    > 如果以上命令给出的文件列表是你想删除的， 那么接下来执行
    > 
    > `git clean -f -d`或者`git clean -fd`就可以了。
    > 
    > 其中-f表示文件-d表示目录, 如果还要删除.gitignore中的文件那么再加上-x (-x对我来说没用）
    > 
    > 如果git submodule中也存在需要删除的文件那么需要再加个-f， 变成git clean -dff

2. 放弃修改
    git checkout -- file可以丢弃工作区的修改：  (总之，就是让这个文件回到最近一次git commit或git add时的状态。)
    
    git reset命令既可以回退版本，也可以把暂存区的修改回退到工作区(撤销add)。当我们用HEAD时，表示最新的版本。
    
    一是确实要从版本库中删除该文件，那就用命令git rm删掉，并且git commit
3. [Git之使用命令行合并多次提交](https://www.jianshu.com/p/e9b6f92ea07c)

## 分支操作
Git鼓励大量使用分支：
    
1. 查看分支：`git branch`   
2. 创建分支：`git branch <name>`   
3. 切换分支：`git checkout <name>`   
4. 创建+切换分支：`git checkout -b <name>`   
5. 合并某分支到当前分支：`git merge <name>`  ![](gitmerge--no-ff.png)   
6. 删除分支：`git branch -d <name>`
7. 分支重命名
    1. 本地分支重命名`Git branch -m oldbranchname newbranchname`
    2. 远程分支重命名 (假设本地分支和远程对应分支名称相同)
        3. 重命名远程分支对应的本地分支: **git branch -m old-local-branch-name new-local-branch-name**
        4. 删除远程分支: **git push origin :old-local-branch-name**
        5. 上传新命名的本地分支: **git push origin new-local-branch-name: new-local-branch-name**


## https vs ssh

`git config --global credential.helper store`记住密码， 防止每次输入用户名密码

## 仓库迁移

度娘了一堆git仓库迁移的内容，一个个都比较麻烦，而且本地下了代码，还要删去库地址，再切换到新库的地址上传。

一般这种操作都只是master分支，其他分支还要一个一个来，后来在51CTO上找了一个文章，简单明了，一下就全搞定了。

包括所有的分支、标签、日志，一个不少。

当然账号对应的事就没办法了。

四行命令：

```bash
git clone --mirror <URL to my OLD repo location>
cd <New directory where your OLD repo was cloned>
git remote set-url origin <URL to my NEW repo location>
git push -f origin
```

## git提交后出现nano界面,解决方法


这个是使用nano进行编辑提交的页面，退出方法为：
Ctrl + X然后输入y再然后回车，就可以退出了
如果你想把默认编辑器换成别的：
方法一、在GIT配置中设置 core.editor: git config --global core.editor "vim"


方法二、编辑~/.gitconfig文件。在core中添加editor = vim。如此以后在使用git的时候就自动使用vim作为编辑器