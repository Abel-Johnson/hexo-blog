---
title: 关于git/github
date: 2016-01-06 23:23:32
tags: [Git]
---
是一种分散性管理控制系统

> 名词解释
fork: 将github里某个仓库**复制**到自己的账户下  

> follow: 关注别人  

> clone: 将已有仓库克隆到本地

> add: 把新建出来的文件添加到(个人理解的)追踪列表里   

> commit: 将代码提交到本地git(记录工作树中所有文件的当前状态)

> push: 将代码同步到github


---
> 集中型版本控制:只有一个仓库,存放在服务器中,集中管理,开发者连接不上服务器或者服务器故障,就会影响编程

> 分散型版本控制: 拥有多个仓库,相对而言较复杂,不过,由于本地开发环境中就有仓库,所以开发者可以不必连接远程仓库就可以进行开发

---
配置步骤:
`$git config --global user.name "Firstname Lastname"`
`$git config --global user.email "your_email@example.com"`
*这两个用户信息只是写入提交日志的,存储在git配置文件.gitconfig中的,和github没有关系*  
`$ git config --global color.ui auto`  
 命令高亮显示,提高可读性
 
 ---
 SHH key:
 github上,连接已有仓库时的认证,是通过使用了SSH的公开密钥(yue)认证的方式进行的,
 创建方法: 
 
```shell
$ ssh-keygen -t rsa -C "your_email@example.com(github使用的邮箱)"   
Generating public/private rsa key pair.  
Enter file in which to save the key  
(/Users/your_user_directory/.ssh/id_rsa): 按回车键  
Enter passphrase (empty for no passphrase): 输入密码  
Enter same passphrase again: 再次输入密码
```
会返回
> Your identification has been saved in /Users/your\_user\_directory/.ssh/id\_rsa.   
> Your public key has been saved in /Users/your\_user\_directory/.ssh/id\_rsa.pub. The key fingerprint is:  
`fingerprint值` your\_email@example.com   The key's randomart image is:  
> +--[ RSA 2048]----+   
> |      .+ +       |   
> | =oO. |

*其中id\_rsa文件是私钥,id\_rsa.pub是公开密钥*

然后把公钥添加入github里,以后就可以用私有密钥进行认证了  
公钥内容查看指令: 
`$ cat ~/.shh/id_rsa.pub`  
也可以把那个文件拖入chrome打开,然后复制

测试是否匹配(是否配置成功)  
`$ ssh -T git@github.com`

配置完成后,就可以使用手里的私钥与github进行认证和通信了

---
配置完成后开始使用  
创建仓库时,如果勾选了用README.md初始化仓库,就可以直接克隆了,但是如果想把本地的git添加进去,就不要勾选,直接手动push

clone(克隆仓库):
`$ git clone xxxx(从github插到仓库的ssh或https地址)`

在本地仓库文件夹新建一个文件后,该文件初始是Untracked files(未被跟踪的文件)的状态
通过`$ git status`可以查看git状态  
`$ git add 新建出来的文件`add指令用来把新建出来的文件添加到追踪列表里(
> 	官方解释: 新建文件只是在工作树中创建了,该文件并不会被记入git仓库的版本管理**对象**中,add命令将其加入到暂存区(提交之前的一个临时区域)

)   
`$ git commit -m "提交描述summary"`提交到本地git   
提交成功后可以通过`$ git log`来查看提交日志

之后通过`$ git push`就可以把代码同步到github(网上)

----
实际操作: 
`git init`---初始化仓库
> `$ mkdir xxx新建文件夹名`----该指令用来新建文件夹(默认建在根目录(mac是username文件夹下))
> `cd xxx`
> `$ git init`
> 该初始化指令成功后会在xxx文件夹里生成一个.git文件夹,里边存储着仓库数据(术语: 附属于该仓库的工作树),文件的新建,编辑等操作在工作树中进行,然后记录到仓库中,以此管理文件的历史快照


`git status`----查看仓库的状态
> 显示git仓库的状态(很常用)
> 只要对git的工作树或者仓库进行操作,该命令的显示结果就会发生变化
> 