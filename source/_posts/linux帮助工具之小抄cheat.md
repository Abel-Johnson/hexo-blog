---
title: linux帮助工具之小抄cheat
date: 2019-07-26 15:33:23
tags: [Linux]
---


## 安装cheat

### Mac 安装

`brew install cheat`

> 可能报错: `Permission denied @ dir_s_mkdir - /usr/local/Frameworks`
>     
> 发现`/usr/local/`下没有路径`/usr/local/Frameworks`
> 
> 需要新建该路径，并修改权限
> 
> 解决：
> 
> ```bash
> $ sudo mkdir /usr/local/Frameworks
> $ sudo chown $(whoami):admin /usr/local/Frameworks
> ```

### Ubuntu/debian

```bash
sudo apt-get install python-pip git
sudo pip install docopt pygments
git clone https://github.com/chrisallenlane/cheat.git
cd cheat
sudo python setup.py install
```




`cheat find`