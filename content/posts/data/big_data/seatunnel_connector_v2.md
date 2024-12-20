---
title: "Seatunnel 连接器 V2 版本"
subtitle: "Seatunnel Connector V2 Features"
date: 2023-05-24T13:51:15+08:00
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
- SeaTunnel
- big data
categories:
- big data

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



## 连接器 V2 与 V1 的区别
自从 https://github.com/apache/incubator-seatunnel/issues/1608 之后添加了`连接器 V2` 功能. 连接器 V2 是基于 Seatunnel Connector API 接口定义的. 和连接器 V1 不同的是, 连接器 V2 支持以下功能: 
* **多引擎支持**: SeaTunnel Connector API 是一套独立与引擎的 API. 在这套 API 的基础上进行研发的连接器 V2  是可以运行在多个引擎上的. 现在是支持 Flink 和 Spark 的, 未来还会支持更多的引擎. 
* **多引擎版本支持**: 通过翻译层 (translation layer) 将连接器与引擎解耦, 解决了大多数连接器需要修改代码才能支持新版本的底层引擎的问题. 
* **统一批处理和流处理**: 连接器 V2 支持批处理和流处理. 不需要分别开发批处理和流处理的连接器. 
* **多路复用的 JDBC/Log 连接器**: 连接器 V2 支持 JDBC 资源重用和共享数据库日志解析. 



## 源连接器特点 (Source)
源连接器拥有一些共同的特点, 并且每种连接器不同程度的支持它们.



### exactly-once (精确一次)
如果数据源中的每条数据只会被源发送到下游一次, 则认为这个源连接器支持只写一次. 

在 SeaTunnel 中, 可以在检查点时将读取的分割 (**Split**) 及其偏移量 (**offset**) (当时拆分中读取数据的位置, 如行号、字节大小、偏移量等) 保存为状态快照 (**StateSnapshot**) . 如果任务重新启动, 我们将获得最后一个状态快照, 然后定位上次读取的分割和偏移量并继续向下游发送数据. 

例如: `File`, `Kafka`



### column projection (列投影)
如果连接器支持仅从数据源读取指定的列 (注意! 如果先读取所有的列, 然后通过 `schema` 过滤不需要的列, 那么这种不是真正的列投影). 

例如 `JDBC 源` 可以用 sql  定义读取的列. 

`Kafka 源` 会读取指定主题的所有内容, 然后使用 `schema` 过滤不需要的列, 这个就不是**列投影**. 



### batch (批)
批处理模式, 数据读取是有边界的, 当所有数据被读取完之后作业就会结束. 



### stream (流)
流处理模式, 数据读取是无边界的, 并且作业是不会停止的. 



### parallelism (并行度)
并行源连接器支持配置 `parallelism`, 每个并行度都回创建一个任务来读取数据. 在并行源连接器中, 源会被拆分为多个, 然后通过枚举器 (enumerator) 分配给源阅读器 (SourceReader) 进行处理. 



### support user-defined split (支持用户定义分割)
用户可以配置分割规则. 



## 接收连接器特点 (Sink)

接收连接器拥有一些共同的特点, 并且每种连接器不同程度的支持它们.



### exactly-once (精确一次)
当任何数据流入分布式系统时, 如果系统在整个处理过程中只对任何一条数据准确地处理了一次, 并且处理结果是正确的, 则认为系统满足了精确的一次一致性. 

对于接收连接器, 如果任何数据只写入目标一次, 则接收连接器支持精确一次. 通常有两种方法可以实现这一点: 
1. 目标数据源支持键消重 (key deduplication), 例如: `MySQL`, `Kudu`. 
2. 目标支持 **XA Transaction** (此事务可以跨会话使用. 即使创建事务的程序已经结束了, 新创建的程序只需要知道上一个事务的 ID 来重新提交或回滚事务). 所以可以使用两段提交 (**Two-phase Commit**) 来确保 **exactly-once**. 例如: `File`, `MySQL`. 



### cdc (change data capture  变更数据捕获)
如果接收连接器支持基于主键的写入行类型 (INSERT/UPDATE_BEFORE/UPDATE_AFTER/DELETE), 那么则认为支持  cdc. 
