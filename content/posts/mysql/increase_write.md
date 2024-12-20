---
title: "MySQL写入速度调优之`innodb_flush_log_at_trx_commit`"
subtitle: "Increase writing speed of Mysql -- `innodb_flush_log_at_trx_commit`"
date: 2023-07-03T11:12:37+08:00
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



之前项目里一直在用的 SeaTunnel 版本是 2.1.3, 有些旧了. 而且 Spark 和 Flink 的脚本还要写两套. 所以就在考虑要升级到 v2.3.2. 所以最近在做  SeaTunnel v2.3.2 的性能调研. 



之前做 v2.3.0 的调研的时候就发现在写入 MySQL 的时候性能特别低, 写入速度竟然 200/s. 有些不能接受... 因为 v2.1.3 写入 MySQL 的速度可以达到 2000+/s. 但是当时也没有深究这个问题, 就搁置了. 最近这个问题又被提起的时候, 就再次去社区找找看有没有类似的问题. 然后发现了也有人面临着这个问题: [issue](https://github.com/apache/seatunnel/issues/4658). 里面提出在链接 URI 后面加上参数 `rewriteBatchStatements=true`. 但是这个参数在这里的提高的并不明显.. 

> 我甚至觉得这里增加这个参数之后, 其实并没有提升. 😂
>
> 这个参数的意义是数据库会更高性能的执行批量处理. 



当写入的时候, 再去查看服务器的时候发现, 硬盘的 IO 有明显的增高. 于是就猜想, 是不是 v2.3.2 版本里, 写入的时候, 每条都作为一个事务提交的, 然后就想到了 `innodb_flush_log_at_trx_commit` 这个参数, 在补充这部分的知识的时候, 发现了也可以调整一下相关的参数`sync_binlog` 以达到调优的目的. 



于是就有了这篇博客记录一下. 



## `innodb_flush_log_at_trx_commit`

这个参数是用来配置 MySQL 日志何时写入硬盘的参数. 



查看 MySQL 配置: 

```sql
SHOW VARIABLES LIKE 'innodb_flush_log_at_trx_commit';
```



* `0`: 日志缓存区将每隔 1 秒写到日志中, 并且将日志文件的数据刷新 (flush) 到磁盘上. 该模式下在事务提交时不会主动触发写入磁盘的操作. 
* `1`: 每次提交事务时, MySQL 都会把日志文件的数据写入, 并且刷新到磁盘上, 默认为该模式.
* `2`: 每次提交事务时, MySQL 都会把日志文件的数据写入, 但是刷新到磁盘的操作不会同时进行, 而是每秒执行一次刷新到磁盘的操作. 

> 所以说:
>
> * 当设置为 `0` 的时候, 速度最快, 但是不安全, `mysqld` 进程的崩溃会导致上一秒所有事务数据的丢失.
>
> * 当设置为 `1` 的时候, 速度最慢, 但是最安全. 在 `mysqld` 服务崩溃或服务器崩溃的情况下, 日志只可能丢失最多一个语句或一个事务. 
>
> * 当设置为 `2` 的时候, 速度快, 也比设置为 `0` 时安全, 只有在操作系统崩溃或系统断电的情况下, 上一秒所有事务数据才可能丢失. 所以我将 MySQL 改成该模式. 



## `sync_binlog`

默认情况下, 并不是每次写入时都将 binlog 日志文件与磁盘同步. 因此如果操作系统或服务器崩溃. 有可能 binlog 中最后的语句丢失. 

为了防止这种情况, 可以使用 `sync_binlog` 全局变量 (`1` 是最安全的值, 但也是最慢的), 使 binlog 在每 N 次 binlog 日志文件写入后与磁盘同步. 



查看 MySQL 配置: 

```sql
SHOW VARIABLES LIKE 'sync_binlog';
```



所以最终, 我将 `innodb_flush_log_at_trx_commit` 设置为 `2`, `sync_binlog` 设置为 `100` . 然后经过测试, v2.3.2 对于 MySQL 的写入速度达到了 2000+/s 的速度. 😄
