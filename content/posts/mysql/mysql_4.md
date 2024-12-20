---
title: "MySQL压力测试"
subtitle: ""
date: 2022-01-10T19:56:53+08:00
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



## 有了数据库之后, 还需要先进行压测

拿到一个数据库之后, 首先得先对这个数据库进行一个较为基本的基准压测. 也就是说, 你得基于一些工具模拟一个系统每秒发出1000个请求到数据库上去, 观察一下他的CPU负载、磁盘IO负载、网络 IO负载、内存复杂, 然后数据库能否每秒处理掉这1000个请求, 还是每秒只能处理500个请求? 这个过程, 就是压测. 



那为什么不等到Java系统都开发完之后, 直接让Java系统连接上MySQL数据库, 然后直接对Java系统进行压测呢? 

因为数据库的压测和它上面的Java系统的压测, 其实是两回事, 首先得知道数据库最大能抗多大压力, 然后再去看Java系统能抗多大压力. 因为有一种可能是, 数据库每秒可以抗下2000个请求, 但是Java系统每秒只能抗下500个请求. 所以不能光是对Java系统去进行压测, 在那之前也得先对数据库进行压测, 做到心里有个数. 



## QPS和TPS到底有什么区别

既然是要压测, 那么肯定得先明白一点, 每秒能抗下多少个请求, 其实是有专业术语的, 分别是`QPS`和`TPS`. 



- `QPS`: Query Per Second. 也就是说数据库每秒可以处理多少个请求, 大致可以理解为一次请求就是一条SQL语句, 也就是说数据库可以每秒处理多少个SQL语句. 

Java系统或者中间件系统在进行压测的时候, 也可以使用这个指标. 

- `TPS`: Transaction Per Second. 指的是每秒可以处理的事务量. 就是说数据库可以每秒处理多少次事务提交或者回滚. 



## IO相关的压测性能指标

1. `IOPS`: 指的是机器的随机IO并发处理能力. 举个🌰: 机器可以达到200 IOPS, 意思就是说每秒可以执行200个随机IO读写请求. 

    这个指标很关键, 因为在之前说过, 在内存中更新的脏数据库, 最后都由后台IO线程在不确定的时间, 刷回到磁盘中去, 这就是随机IO的过程. 也就是说, 如果IOPS指标太低, 那么就会导致内存里的脏数据库刷回磁盘的效率不高. 

2. **吞吐量**: 指的是机器的磁盘存储每秒可以读写多少字节的数据量. 

    这个指标也很关键, 之前也说过, 在执行各种SQL语句的时候, 提交事务的时候, 其实都是大量的会写redo log之类的日志的, 这些日志都会直接写磁盘文件. 所以一台机器的存储每秒可以读写多少字节的数据量, 就决定了他每秒可以把多少redo log之类的日志写入到磁盘里去. 

    一般来说, 我们写redo log之类的日志, 都是对磁盘文件进行顺序写入的, 也就是一行接着一行的写, 不会说进行随机的读写, 那么一般普通磁盘的顺序写入的吞吐量每秒都可以达到200MB左右. 

    所以通常而言, 机器的磁盘吞吐量都是足够承载高并发请求的. 

3. `latency`: 指的是往磁盘里写入一条数据的延迟.

    这个指标同样重要, 因为执行SQL语句的和提交事务的时候, 都需要顺序写redo log磁盘文件, 所以此时写一条日志到磁盘文件里去, 到底是延迟1ms, 还是延迟100us, 这就对数据库的SQL语句执行性能是有影响的. 



## 压测的时候要关注的其他性能指标

1. **CPU负载**: 这个也是一个很重要的指标. 因为假设数据库压测到了3000 QPS, 可能其他指标都还正常, 但是此时CPU负载特别高, 那么也说明你的数据库不能继续往下压测更高的QPS了, 否则CPU是吃不消的. 

2. **网络负载**: 这个就是看机器带宽情况下, 在压测到一定的QPS和TPS的时候, 每秒钟机器的网卡会输入多少MB数据, 会输出多少MB数据. 因为有可能网络带宽最多每秒传输100MB的数据, 那么可能QPS到1000的时候, 网卡就打满了, 已经每秒传输100MB的数据了, 此时即使其他指标还正常, 也不能继续压测下去了. 

3. **内存负载**: 这个就是看压测到一定情况下的时候, 机器内存损耗了多少, 如果说机器内存损耗过高了, 说明也不能继续压测下去了. 



## 推荐压测工具

`sysbench`, 这个工具可以自动帮你在数据库里构建出来大量的数据. 然后也可以模拟几千个线程并发的访问数据库, 模拟各种sql语句, 包括各种书屋提交到数据库里, 甚至可以模拟出几十万的TPS对数据库进行压测. 
