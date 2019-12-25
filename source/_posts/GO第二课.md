---
title: GO第二课
date: 2019-11-13 10:42:51
tags: [Go]
---

[TOC]

## 项目分层

### MVC 和 controller service dao

DAO = Data Access Object = 数据存取对象
service 服务的特征：抽象、独立、稳定。
数据库服务 存储服务...
业务模块符合这些特征也可以是服务,叫做业务服务(business service)
不推荐在 Service 里调用多个 Dao，推荐在 Service 里调用多个 Service，

MVC

Model = 模型, 就是数据的抽象, 整个系统就是数据的流动
View = 视图 前后端没有分离的时候, 视图也需要后端
Controller =


### 数组与切片的区别
### 包 模块 ...
### go module是个啥
### 字符串格式化
### log包, 后端日志系统
### c.get/set/getInt64
### 中间件 前置过滤器 https://blog.csdn.net/weixin_34072458/article/details/91394485
### mongo
### 指针