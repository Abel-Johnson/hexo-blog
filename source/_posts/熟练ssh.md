---
title: 熟练ssh
date: 2021-06-07 15:20:46
tags: [SSH]
---

[TOC]

# Hold住SSH

> 之前一直有一个困扰, **经常发生  就是git push 或者 clone的时候 我都需要手动在命令行执行`ssh-add ~/ssh/id-rsa__xng`这个命令**  
> 我当时猜测大概是因为我有三个git相关的平台登录需求:  github两个账号  gitlab一个账号  
> 所以他们会需要手动切换  
> 但后来一直没有深究, 一知半解状态持续到今天,   
> 终于忍不了了, 决定"**搞透他**"  
> [参考: http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html)

## ssh

> 本质: 一个协议  
> 用途: 计算机之间的登录(特别的, 会加密, 所以安全)

### 语法

- `$ ssh user@host`
- `$ ssh host` (user一样的时候可以省略)
- `$ ssh -p 2222 user@host`

### 机制

非对称加密: 公钥加密, 私钥解密; 私钥加密, 公钥解密

1. 远程主机收到ssh登录请求以后, 把自己的公钥发回去
2. 本地机器使用这个公钥加密登录密码, 发过去
3. 远程主机私钥解密,得到登录密码. 比对鉴权

#### 隐患

[中间人攻击](http://en.wikipedia.org/wiki/Man-in-the-middle_attack)  
他会截获(比如公共场所的wifi)用户的ssh登录请求, 把黑客公钥发给用户, 这样相当于用户把自己的重要信息交给了敌人的间谍, 敌人公私钥都有, 这样就可以拿到你的登录密码, 然后去登录主机, 这样就危险了
任何人都可以创建公钥, 不像https有CA(证书中心)公证, 所以没办法判断一个公钥到底安全不安全

#### 解法
##### 密码登录
   
1. 第一次登录的时候, 都有一个提示:
    ```shell
    $ ssh user@host
    The authenticity of host 'host (12.18.429.21)' can't be established.
    RSA key fingerprint is 98:2e:d7:e0:de:9f:ac:67:28:c2:42:2d:37:16:58:4d.
    Are you sure you want to continue connecting (yes/no)?
    ```
   会提示你, 这是一个未知的主机, 他的公钥指纹是xxx, 你确定要链接吗?
   用户需要做的是 用这个指纹去比对远程主机在自己网站上公示的公钥指纹, 核对一致后, 就放心了  敲yes
2. 系统会出现一句提示，表示host主机已经得到认可。
    ```shell
    Warning: Permanently added 'host,12.18.429.21' (RSA) to the list of known hosts.
    ```
   > 当远程主机的公钥被接受以后，它就会被保存在文件$HOME/.ssh/known_hosts之中。下次再连接这台主机，系统就会认出它的公钥已经保存在本地了，从而跳过警告部分，直接提示输入密码。

3. 然后，会要求输入密码。
    ```shell
    Password: (enter password)
    ```
##### 公钥登录

可以实现免密码登录

原理:
登录的本质就是 远程主机识别出来你是可以信赖的
这种方式步骤就是
1. 用户机器公钥放在远程服务器里, (比如一直做的github ssh块填写的本地公钥)
2. 登录的时候 远程主机会发一段`随机字符串A`, 用户收到以后, 把 `自己私钥加密(随机字符串A)`发回去, 远程主机用存好的公钥解密对照, 一致就允许免密码直接登录

自己的公钥没有可以生成 `ssh keygen`

> 运行上面的命令以后，系统会出现一系列提示，可以一路回车。其中有一个问题是，要不要对私钥设置口令（passphrase），如果担心私钥的安全，这里可以设置一个。

运行结束以后，在$HOME/.ssh/目录下，会新生成两个文件:  
`id_rsa.pub`和`id_rsa`。前者是你的公钥，后者是你的私钥。

这时再输入下面的命令，将公钥传送到远程主机host上面：  
`$ ssh-copy-id user@host`

好了，从此你再登录，就不需要输入密码了。

_- 如果还是不行 ---_  
就打开远程主机的/etc/ssh/sshd_config这个文件，检查下面几行前面"#"注释是否取掉。
```shell
RSAAuthentication yes  
PubkeyAuthentication yes  
AuthorizedKeysFile .ssh/authorized_keys
 ```
  
然后，重启远程主机的ssh服务。  
```shell
// ubuntu系统  
service ssh restart  
// debian系统  
/etc/init.d/ssh restart
```
  
###### authorized_keys文件

远程主机将用户的公钥，保存在登录后的用户主目录的`$HOME/.ssh/authorized_keys`文件中。公钥就是一段字符串，只要把它追加在`authorized_keys文件`的末尾就行了。

这里不使用上面的`ssh-copy-id`命令，改用下面的命令，解释公钥的保存过程：
```shell
$ ssh user@host 'mkdir -p .ssh && cat >> .ssh/authorized_keys' < ~/.ssh/id_rsa.pub
```

这条命令由多个语句组成，依次分解开来看：（1）"$ ssh user@host"，表示登录远程主机；（2）单引号中的mkdir .ssh && cat >> .ssh/authorized_keys，表示登录后在远程shell上执行的命令：（3）"$ mkdir -p .ssh"的作用是，如果用户主目录中的.ssh目录不存在，就创建一个；（4）'cat >> .ssh/authorized_keys' < ~/.ssh/id_rsa.pub的作用是，将本地的公钥文件~/.ssh/id_rsa.pub，重定向追加到远程文件authorized_keys的末尾。

写入authorized_keys文件后，公钥登录的设置就完成了。


## ssh-add
ssh-add 
作用: 
这个命令不是用来永久性的记住你所使用的私钥的。
实际上，它的作用只是把你指定的私钥添加到 ssh-agent 所管理的一个 session 当中。而 ssh-agent 是一个用于存储私钥的**临时性**的 session 服务，
也就是说当你重启之后，ssh-agent 服务也就重置了。
(这也就解释了为什么我以前重新add的频次不规律, 因为重启不规律, 每次重启以后都要重新add , 但是github这些貌似没关系)

如果是为了**永久记住**对应的私钥是哪个，我们不能依赖 ssh-agent 服务。能依赖什么则取决于以下哪些方案适合你的使用场景。

使用某种安全的秘钥管理机制
Mac 系统内置了一个 **Keychain** 的服务及其管理程序，可以方便的帮你管理各种秘钥，其中包括 ssh 秘钥。
ssh-add 默认将制定的秘钥添加在当前运行的 ssh-agent 服务中，但是你可以改变这个默认行为让它添加到 keychain 服务中，让 Mac 来帮你记住、管理并保障这些秘钥的安全性。

你所要做的就是执行下面的命令：

`$ ssh-add -K [path/to/your/ssh-key]`
之后，其他的程序请求 ssh 秘钥的时候，会通过 Keychain 服务来请求。下面的截图里你可以看到我当前的机器上 Keychain 为我管理的有关 ssh 的秘钥，这其中包括我自己生成的四个，以及 Github Client App 自己使用的一个——前者几个都是供 ssh 相关的命令所使用，而后者则指明了仅供 Github.app 这个应用程序使用。 另外，它们都是 login keychains 也就是只有当前用户登录之后才会生效的，换了用户或是未登录状态是不能使用的，这就是 Keychain 服务所帮你做的事情。

![Mac Keychain Access](https://segmentfault.com/img/bVdOio)

如何使用多个 ssh 秘钥并对应不同的应用程序？
这个问题也是我没有完全吃透的，按照某些资料描述，做了以上的工作之后，应用程序应该能够自动匹配适用的 ssh 秘钥了。但是在我学习的过程中也遇到过非得手动指定的情况（那个时候我还不了解 Keychain 的作用，都是手动去 ssh-add 的），于是另外一种机制可以帮你解决这个问题，即 ssh config。

一言蔽之，ssh config 就是一个配置文件，描述不同的秘钥对应的设置——包括主机名、用户名、访问策略等等。

以下我截取了本地配置的两个片段：

![ssh config 1](https://segmentfault.com/img/bVdQ2u)
![ssh config 2](https://segmentfault.com/img/bVdQ4m)

这两段配置分别对应 Github 和 Coding 这两个服务所使用的秘钥。第一行的 Host 只是一个名字，第三行的 Hostname 才是对应的真实地址，但是两者最好保持一致，这样不用在脑袋里转换。

用这样的配置，当我 git clone https://github.com/user/repo 的时候，id_rsa 秘钥会被使用，当我 git clone https://coding.net/user/repo 的时候，很显然 nightire 秘钥会被使用。

当然，此配置不局限于 Git，所有底层使用 SSH 的应用和命令都会遵照配置文件的指示来找到对应的私钥。

回到本节开始的话题，我相信有了 Keychain 做管理应该是不需要这个配置文件的，但是我还没有机会去做测试。目前的环境一切正常，等到我换新机器重新配置环境的时候我会试一试看。

关于 Host 和 Hostname 的对应关系，如果 Hostname 是域名则最好保持一致。但是这里有两个诀窍：
1. 如果同一域名下有两个不同的配置怎么办？
以 Github 为例，如果我有两个账户，一个个人的，一个组织的，并且要使用不同的秘钥，那么我可以这么写：
图片描述
这里 Host 后面对应的是 Github 的两个用户名，也就是 github.com/nightire 和 github.com/very-geek
2. 如果域名是数字 IP，是否可以简化呢？
Host 可以帮助你把对应的 IP 变成好记的名字。比如说我在公司内部配置了 Git Server（基于 gitolite 或 Gitlab 或任何工具），正常的访问地址是：git://xxx.xxx.xxx.xxx:repo.git，如下的配置则可以帮你把它简化成：git.visionet:repo.git
图片描述
非常有用。

有没有简单点的办法？
有。如果 ssh-add 已经可以满足你的要求（除了启动以后还要再来一遍以外），那么你完全可以用脚本自动化这件事。简单地把你输入的 ssh-add 命令的内容写进 .bashrc 或 .bash_profile（或其他任何你使用的 shell 环境配置文件）中去，这样只要你打开终端，就等于自动做了这件事情。

不过如我之前所说，这个机制是依赖 ssh-agent 服务的，并且只能在终端下有效。而用 Keychain 机制的话，是整个系统内都有效的（包括不依赖终端的应用程序）并且无需开启 ssh-agent 服务。

最后 Keychain 服务不是只有 Mac 才有的，我刚才搜索了一下，Windows 和 各种 Linux 都有对应的机制，不过我没用过，只能以 Mac 为例了。了解了这些概念，相信你可以自己查得到具体的方法。





===

这是我之前的:
```shell
#这里是原先使用的帐号(canny09@qq.com)
Host github.com
HostName github.com
User git
IdentityFile ~/.ssh/id-rsa__abel-johnson


#这里是新的帐号(listen_kb@163.com)
Host aj1219
HostName github.com
User git
IdentityFile ~/.ssh/id-rsa__aj1219


Host git@xgit.xiaoniangao.cn
HostName xgit.xiaoniangao.cn
User git
IdentityFile ~/.ssh/id-rsa__xng
#更改[remote "origin"]项中的url中的#my.github.com 对应上面配置的host[remote "origin"] url = git@my.github.com:itmyline/blog.git

```

改改
```shell
#这里是原先使用的帐号(canny09@qq.com)
Host github.com-Abel-Johnson
HostName github.com
User git
IdentityFile ~/.ssh/id-rsa__abel-johnson


#这里是新的帐号(listen_kb@163.com)
Host github.com-AJ1219
HostName github.com
User git
IdentityFile ~/.ssh/id-rsa__aj1219


Host xgit.xiaoniangao.cn
HostName xgit.xiaoniangao.cn
User git
IdentityFile ~/.ssh/id-rsa__xng
#更改[remote "origin"]项中的url中的#my.github.com 对应上面配置的host[remote "origin"] url = git@my.github.com:itmyline/blog.git

```
