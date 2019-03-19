---
title: 在Mac上搭建本地Apache服务器一些注意点
date: 2017-01-06 23:23:32
tags: [Mac, Apache]
---

一般在开发ios程序中，我们需要使用到和服务器的交互操作。

一般我们在Mac上使用Apache来搭建服务器。数据库采用MySQL。在Mac中Apache是自带的。所有，我们可以不需要额外的去Apple store下载。

我们可以直接通过命令行的形式来，开启服务器。

在终端中开启Apache的指令是：

sudo apachectl -k start   //这里要特别说明是，必须以root的身份来启动服务器（下同）
//重启Apache指令
sudo apachectl -k restatr
开启后，在Safari中输入localhost。HTML页面显示为“it  work”，表明此时Apache 服务器已经开启了。
其中默认的文件首页在"/Library/WebServer/Documents/index.html"中，
如果我们要自定义站点的路径可以如下操作
1.打开终端  
   输入  cd  /etc/apache2    进入到配置apache目录
   输入  sudo vim httpd.conf  //Apache配置文件,打开vim编辑器  
   在vim编辑器中
   输入 查找命令
          /DocumentRoot   //找到默认文件路径  DocumentRoot "/Library/WebServer/Documents"该目录普通用户没有写入权限，需要输入管理员密码
   在这里我们利用点击键盘i键，从命令模式进去到vim的编辑模式， 我们可以改成 DocumentRoot "/Users/aple/Sites" //其中aple代表用户名，Sites表示站点文件夹。
    再次输入查找命令
         /DocumentRoot  //找到<Directory "/Library/WebServer/Documents">,改成自定义路径。如下图所示。

  如果需要浏览该文件夹下的内容，可以在</Directory>前添加：

    Options Indexes


      这样配置就完成了。
在默认情况下。服务器对PHP支持是关闭的，如果要打开对PHP的支持，应该进行如下操作
打开终端输入
cd /etc/apache2
sudo vim httpd.conf
/php
按字母x，删除#，然后按ESC，输入:wq退出编辑器
cd /etc/
sudo cp php.ini.default php.ini    //复制命令将php.ini.default复制为php.ini。
重新启动Apache服务器
这样就打开了支持PHP的功能。
另外补充一点。可能用到的vim命令
vim中有两种模式

命令模式，用于输入命令(注意输入法要在英文状态)

(1)shift+V  可以选中一行
(2)y  复制一行
(3)p  在当前行下方粘贴复制的内容
(4)d$  删除到行尾
(5)x  删除一个字符
(6):wq  保存退出
(7):q!  不保存退出
(8):set nu  显示行号
(9) i 进入编辑模式
 编辑模式：用于编辑按ESC可以切换回命令模式

重启服务器时会出现：httpd: Could not reliably determine the server's fully qualified
那是因为没有配置localhost端口为80
进入vim，加入  ServerName localhost:80即可解决