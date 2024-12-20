---
title: "MySQL总览"
subtitle: ""
date: 2021-12-13T16:31:37+08:00
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



## 什么是`CRUD`?

`CRUD`是指在做计算处理时的增加(`Create`), 读取查询(`Retrieve`), 更新(`Update`)和删除(`Delete`). 主要是被用在描述软件系统中**DataBase**或者持久层的基本操作. 



## 什么是数据库驱动?

想要访问数据库, 就需要和数据库建立一个网络连接. 那么, 建立这个网络连接的就是数据库驱动. 所以对于**MySQL**来说, 对应每种常见的编程语言(e.g. **Java**, **PHP**, **.NET**, **Python**, **Ruby**等), **MySQL**都会提供对应语言的**MySQL驱动**. 其实**数据库驱动**就是中间件. 



## 数据库连接池是用来干嘛的? 

首先要知道的是, 一个系统和数据库建立的连接往往都不止一个. 但是每次访问数据库的时候都建立一个连接, 然后执行**SQL语句**, 然后再销毁这个连接, 这种方式显然是不合适的. 因为每次建立一个**数据库连接**都很耗时, 效率会很低下. 所以, 就出现了**数据库连接池**这个东西. 一个**数据库连接池**里会维持多个连接, 让多个线程使用里面的不同的**数据库连接**去执行**SQL语句,** 执行完语句之后, 不是销毁这个连接, 而是把它放回池子里, 后面还能继续用. 



常见的**数据库连接池**有`DBCP`, `C3P0`, `Druid`



## MySQL是如何执行一条SQL语句的?



### 第一步 线程: 接收SQL语句

首先, 假设数据库服务器的连接池中的某个连接收到了**网络请求**, 假设就是一条**SQL语句**, 这个工作一定是一个**线程**来进行处理的, 来监听请求以及读取请求数据. 当**MySQL**的工作线程接收到**SQL语句**之后, 会转交给**SQL接口**去执行. 

**SQL接口(SQL Interface)**就是**MySQL**内部提供的一个组件, 是一套执行**SQL语句**的接口. 



### 第二步 SQL接口: 解析SQL语句

那么, **SQL接口**又是如何执行**SQL语句**的呢? 比如要执行下面这条语句: 

```sql
SELECT id, name, age FROM users WHERE id = 1;
```

**MySQL**本身也是一个系统, 是一个**数据库管理系统(DBMS)**, 是没法直接理解这些语句的, 所以就需要**查询解析器(Parser)**出场了! 这个查询解析器是负责对SQL语句进行解析的, 比如上面的语句拆解一下, 可以拆解为一下几个部分: 

1. 我们现在要从`users`表里查询数据
2. 查询`id`字段的值等于`1`的那行数据

1. 对查出来的哪行数据要提取里面的`id, name, age`三个字段

所谓的**SQL解析**, 就是按照既定的SQL语法, 对我们按照SQL语法规则编写的SQL语句进行解析, 然后理解这个SQL语句要干什么事情. 



### 第三步 查询优化器: 选择最优查询路径

当通过解析器理解了SQL语句要干什么之后, 接着就会找**查询优化器**(Optimizer)来选择一个最优的查询路径. 



### 第四步 存储引擎: 调用存储引擎接口, 真正执行SQL语句

最后一步, 就是把查询优化器选择的最有查询路径交给底层的存储引擎去真正的执行. 



**存储引擎**是MySQL架构设计中很有特色的一个环节. 在真正执行SQL语句的时候, 要不是更新数据, 要不是查询数据, 但是具体的数据是存放在内存里还是在磁盘里呢? 这个时候就需要**存储引擎**了, 存储引擎其实就是执行SQL语句的, 它是按照一定的步骤去查询内存缓存数据, 更新磁盘数据, 查询磁盘数据等等, 执行诸如此类的一系列的操作. 



MySQL的架构设计中, SQL接口, SQL解析器, 查询优化器其实都是通用的, 就是一套组件而已. 但是是支持各种各样的存储引擎的, 比如常见的`InnoDB`, `MyISAM`, `Memory`等等, 我们是可以选择使用哪种存储引擎来负责具体的SQL语句执行的. 



### 第五步 执行器: 根据执行计划调用存储引擎的接口

现在回过头来看一个问题, 存储引擎可以访问内存和磁盘上的数据, 那么是谁来调用存储引擎的接口呢? 



其实还漏了一个**执行器**的概念, 执行器会根据优化器选择的执行方案, 去调用存储引擎的接口按照一定的顺序和步骤, 就把SQL语句的逻辑给执行了. 



举个🌰: 比如执行器可能会先调用存储引擎的一个接口, 去获取`users`表中的第一行数据, 然后判断一下这个数据的`id`字段的值是否等于我们期望的值, 如果不是的话, 就继续调用存储引擎的接口, 去获取`users`表的下一行数据. 



基于上述的思路, **执行器就会去根据优化器生成的一套执行计划, 然后不停的调用存储引擎的各种接口去完成SQL语句的执行计划**, 大致就是不停的更新或者提取一些数据出来. 
