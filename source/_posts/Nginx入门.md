---
title: Nginx入门
date: 2019-12-25 14:58:11
tags: [Nginx]
---

## 参考

[8分钟带你深入浅出搞懂Nginx](https://zhuanlan.zhihu.com/p/34943332)
[写的很好的教程mac下安装nginx](https://www.cnblogs.com/meng1314-shuai/p/8335140.html)
[nginx 基本入门](https://zhuanlan.zhihu.com/p/24382606)
[Nginx 配置详解-菜鸟](https://www.runoob.com/w3cnote/nginx-setup-intro.html)

## 概念解析

### 并发

### 事件驱动

### 正向代理

![](https://pic4.zhimg.com/80/v2-c8ac111c267ae0745f984e326ef0c47f_hd.jpg)
c => 通过vpn代理 => 访问google

特点: 

1. 代理的对象是客户端
2. 客户端知道目标
3. 目标不知道客户端是通过vpn代理访问的


### 反向代理 

![](https://pic2.zhimg.com/80/v2-4787a512240b238ebf928cd0651e1d99_hd.jpg)

外网访问百度时, 分配到内网上

特点:

1. 代理的是服务器端, 所以叫"**反向**"
2. 这个过程对客户端不可见(透明)




结论: 在涉及到多台机器分配请求时才用得到


## 具体解析


`brew search nginx`

```
Docroot is: /usr/local/var/www

The default port has been set in /usr/local/etc/nginx/nginx.conf to 8080 so that
nginx can run without sudo.

nginx will load all files in /usr/local/etc/nginx/servers/.

To have launchd start nginx now and restart at login:
  brew services start nginx
Or, if you don't want/need a background service you can just run:
  nginx
```


里面有几个关键的路径:

1. Docroot is: /usr/local/var/www
2. /usr/local/etc/nginx/nginx.conf
3. nginx will load all files in /usr/local/etc/nginx/servers/.
4. open /usr/local/Cellar/nginx  //其实这个才是nginx被安装到的目录



安装完 配置一个目录, 可执行文件一个目录,  可执行文件所在的目录还有一个html的快捷方式(指向了配置文件所在文件夹下的www文件夹)

配置里一个典型的格式

```conf
server {
　　　　 #侦听8080端口
    listen       8080;
　　　　 #定义使用 localhost访问
    server_name  localhost;

    #charset koi8-r;

    #access_log  logs/host.access.log  main;

    location / {
　　　　　　　#定义服务器的默认网站根目录位置
        root   html;
　　　　　　  #定义首页索引文件的名称
        index  index.html index.htm;
    }
　　　　　...
　　　　　...
　　　　　... (注释代码太多，就不全部贴出来了)

include servers/*;
```

## 用处

### 虚拟主机web server
#### - 静态服务器
Java 中的资源可以分为动态和静态，动态需要经过 Tomcat 解析之后，才能返回给浏览器，例如 JSP 页面、Freemarker 页面、控制器返回的 JSON 数据等，都算作动态资源，动态资源经过了 Tomcat 处理，速度必然降低。对于静态资源，例如图片、HTML、JS、CSS 等资源，这种资源可以不必经过 Tomcat 解析，当客户端请求这些资源时，之间将资源返回给客户端就行了。此时，可以使用 Nginx 搭建静态资源服务器，将静态资源直接返回给客户端。


### 反向代理【proxy_pass】
在location这一段配置中的root替换成proxy_pass即可。root说明是静态资源，可以由Nginx进行返回；而proxy_pass说明是动态请求，需要进行转发，比如代理到Tomcat上。

equest -> Nginx -> Tomcat，那么对于Tomcat而言，请求的IP地址就是Nginx的地址，而非真实的request地址

### 负载均衡【upstream】
第一，通过upstream来定义一组Tomcat，并指定负载策略（IPHASH、加权论调、最少连接），健康检查策略（Nginx可以监控这一组Tomcat的状态）等。

第二，将proxy_pass替换成upstream指定的值即可。


注意: 用户状态的保存问题，如Session会话信息，不能在保存到服务器上。
### 缓存
在配置上就是一个开启，同时指定目录，让缓存可以存储到磁盘上


## 命令

nginx -c /usr/local/nginx/conf/nginx.conf  # 指定配置文件运行
nginx -t                   # 检查配置文件nginx.conf的正确性
nginx -s reload            # 重新载入配置文件
nginx -s reopen            # 重启 Nginx
nginx -s stop              # 停止 Nginx

