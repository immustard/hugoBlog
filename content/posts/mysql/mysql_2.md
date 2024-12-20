---
title: "InnoDB存储引擎的架构设计"
subtitle: ""
date: 2021-12-23T16:53:11+08:00
draft: false
author:
  name: Mustard	
  link: https://www.buli-home.cn
  email: mustard_gxg@foxmail.com
  avatar: https://pub-7360a7072ee341a58e1e9b6541edca66.r2.dev/portrait/mustard.png
author: ""
authorLink: ""
description: ""
keywords: ""
license: ""
comment: false
weight: 0

tags:
- database
- MySQL
categories:
- MySQL

hiddenFromHomePage: false
hiddenFromSearch: false

summary: ""
resources:
- name: featured-image
  src: featured-image.jpg
- name: featured-image-preview
  src: featured-image-preview.jpg

toc:
  enable: true
math:
  enable: false
lightgallery: false
seo:
  images: []

# See details front matter: /theme-documentation-content/#front-matter
---

<!--more-->



## 什么是`InnoDB`?

InnoDB是第一个提供外键约束的存储引擎, 而且它对事务的处理能力是其它存储引擎无法与之比拟的. 

MySQL在5.5版本之后, 默认存储引擎由`MyISAM`修改为`InnoDB`. 目前, `InnoDB`是最重要的, 也是使用最广泛的存储引擎. 



#### 1. `InnoDB`优势: 

1. 支持事务安装
2. 灾难恢复性好

1. 使用行级锁
2. 实现了缓冲处理

1. 支持外键
2. 适合需要大型数据库的网站



#### 2. 物理存储

1. 数据文件(表数据和索引数据): 

- 共享表空间
- 独立表空间

1. 日志文件



## 更新语句在MySQL中是如何执行的? 

首先假设有一条语句是这样的:

```sql
UPDATE users SET name='xxx' WHERE id=10
```

这条语句是如何执行的呢? 首先肯定是系统通过一个数据库连接发送到了MySQL上, 然后经过SQL接口、解析器、优化器、执行器几个环节, 解析SQL语句, 生成执行计划, 接着由执行器负责这个计划的执行, 调用InnoDB存储引擎的接口去执行. 



## InnoDB的重要内存结构: 缓冲池

前面提到了InnoDB的一个优势就是"实现了缓冲处理", 就是通过InnoDB存储引擎中的一个非常重要的放在**内存**里的组件实现的, 就是**缓冲池(Buffer Pool)**. 这个里面会存很多数据, 便于以后的查询, 要是缓冲池里有数据, 就不会去磁盘查询. 



所以当执行上面那条更新语句的时候, 就会现将`id=10`这一行数据看看是否在缓冲池里, 如果不在的话, 那么会直接从磁盘里加载到缓冲池里来, 而且还会对这行记录**加锁.** 



## undo日志文件: 如何让你更新的数据可以回滚

接着下一步, 假设`id=10`这行数据的`name`原来是`"zhangsan"`, 现在我们更新为`"xxx"`, 那么此时得现将要更新的原来的值`"zhangsan"`和`id=10`这些信息, 写入到undo日志文件中去. 



其实大家都知道, 如果执行一个更新语句是在一个事务里的话, 那么事务提交之前我们都是可以对数据进行**回滚(rollback)**的. 



## 更新buffer pool中的缓存数据

当我们把要更新的那行记录从磁盘文件加载到缓冲池, 也对其进行加锁之后, 并且还把更新前的旧值写入undo日志文件之后, 就可以正式开始更新这行记录了. 更新的时候, 先是会更新缓冲池中的记录, 此时这个数据就是**脏数据**了. 

为什么是脏数据: 因为此时的磁盘中`id=10`这行数据的`name`还是`"zhangsan"`, 还不是`"xxx"`. 



## Redo Log Buffer: 万一系统宕机, 如何避免数据丢失

如果按照上面的操作进行更新, 现在已经把内存里的数据进行了修改, 但是磁盘上的数据还没有修改. 就在这个时候, 系统宕机了, 该怎么办? 



这个时候就必须要把对内存所做的修改写入到一个`Redo Log Buffer`里去, 这也是一个内存的缓冲区, 用来存放redo日志的. 

redo日志用来记录对什么记录进行了修改, 比如对`id=10`这行记录修改了`name`为`"xxx"`, 这就是一个日志. 



这个redo日志其实就是用来在MySQL突然宕机的时候, 用来恢复更新过的数据的. 



## 如果还没提交事务, MySQL宕机了怎么办

如果还没有提交事务, 那么此时如果MySQL崩溃, 必然导致内存里Buffer Pool中的修改过的数据都丢失, 同时写入Redo Log Buffer中的redo日志也会消失. 



其实此时数据丢失是不要紧的, 因为一个更新语句, 没提交事务, 就代表还没有执行成功, 此时MySQL宕机虽然导致内存里的数据都丢失了, 但是会发现, 磁盘上的数据怡然居还停留在原样子. 



换句话说, `id=10`那行数据的`name`字段的值还是旧值`"zhangsan"`, 所以此时这个事务就是执行失败了, 没能成功完成更新, 会收到一个数据库的异常. 然后当MySQL重启之后, 数据并没有任何变化. 



所以, 如果还没提交事务时, MySQL宕机了, 不会有任何问题. 



## 提交事务的时候将redo日志写入磁盘中

接着俩要提交一个事务了, 此时就会根据一定的策略把redo日志从redo log buffer里刷入到磁盘文件里去. 



这个策略是通过`innodb_flush_log_at_trx_commit`来配置的, 它有几个选项. 

- 当这个参数的值为`0`的时候, 那么当提交事务的时候, 不会把redo log buffer里的数据刷入磁盘文件, 此时可能都提交事务了, 结果MySQL宕机了, 然后内存里的数据全部丢失了. 这就相当于提交事务成功了, 但是由于MySQL宕机, 导致内存中的数据和redo日志都丢失了. 
- 当这个参数的值为`1`的时候, 那么当提交事务的时候, 就必须把redo log buffer从内存刷入到磁盘文件里去, 只有事务提交成功, 那么redo log就必然在磁盘里了. 所以只有提交事务成功之后, redo日志一定在磁盘文件里. 

也就是说, 哪怕此时buffer pool中更新过的数据还没刷新到磁盘里去, 此时内存里的数据已经是更新过`name="xxx"`, 然后磁盘上的数据还是没更新过的`name="zhangsan"`. 当MySQL重启之后, 可以根据redo日志去恢复之前做过的修改. 

- 当这个参数的值是`2`的时候, 那么当提交事务的时候, 把redo日志写入磁盘文件对应的os cache里去, 而不是直接进入磁盘文件, 可能1秒之后才会吧os cache里的数据写入到磁盘文件里去. 这种模式下, 提交事务之后, redo log可能仅仅停留在os cache内存缓存里, 没实际进入磁盘文件, 玩意此时要是机器宕机了, 那么os cache里的redo log就会丢失, 同样会感觉提交事务了, 但是结果数据丢了. 
