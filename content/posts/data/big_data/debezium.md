---
title: "Debezium"
subtitle: ""
date: 2022-07-28T16:49:12+08:00
draft: true
author:
  name: Mustard	
  link: https://www.buli-home.cn
  email: mustard_gxg@foxmail.com
  avatar: https://cdn.jsdelivr.net/gh/immustard/gallery/Portrait.png
author: ""
authorLink: ""
description: ""
keywords: ""
license: ""
comment: false
weight: 0

tags:
- draft
categories:
- draft

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



> 官方文档: [传送门](https://debezium.io/documentation/)
>
> 该文档基于 Debezium v1.9 编写



## Debezium概述

Debezium 是一组用来捕获数据库的变化的分布式服务, 以便应用程序可以看到这些更改并作出响应. Debezium 将每个数据库中的所有行级更改记录在更改*事件流*中, 应用程序只需要读取这些流即可按照它们发生的顺序查看更改事件. 



Debezium 是构建于 Apache Kafka 之上的, 并且提供了一系列 Kafka Connect 兼容的连接器. 每一个连接器都与特定的数据库管理系统(DBMS)一起使用. 连接器通过在发生更改时检测更改并将每个更改时间的记录流式传输到 Kafka 主题来记录 DBMS 中数据更改的历史记录. 然后应用程序可以从 Kafka 主题中读取结果事件记录. 



通过利用 Kafka 可靠的流数据平台, Debezium 使应用程序能够正确、完整地使用数据库中发生的更改. 即使您的应用程序意外停止或失去连接, 它也不会错过中断期间发生的事件. 应用程序重新启动后, 它会从停止的位置继续读取主题. 



## Debezium架构

最常见的部署 Debezium 的方式就是用 Apache Kafka Connect. Kafka Connect 是一个用于实现和操作的框架和运行时: 

* Source Connector, 例如 Debezium 将数据发送到 Kafka
* Sink Connector, 从 Kafka 主题中传递数据到其他系统

下面这张图展示了基于 Debezium 的变更数据捕获管道的架构: 

<center>     <img style="border-radius: 0.3125em;     box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"      src="https://cdn.jsdelivr.net/gh/immustard/gallery/pictures/202208080958590.png" width = "65%" alt="" onclick="window.open(this.src)"/>     <br>     <div style="color:orange; border-bottom: 1px solid #d9d9d9;     display: inline-block;     color: #999;     padding: 2px;">       Debezium的数据捕获   	</div> </center>

如图所示, 部署了用于 MySQL 和 PostgresSQL 的 Debezium 连接器来捕获这两种类型的数据库的更改. 每个 Debezium 连接器都建立到其源数据库的连接: 