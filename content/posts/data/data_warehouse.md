---
title: "数据仓库"
subtitle: "Data Warehouse"
date: 2022-06-14T13:29:39+08:00
draft: true
author: ""
authorLink: ""
authorEmail: ""
description: ""
keywords: ""
license: ""
comment: false
weight: 0

tags:
- data
- data warehouse
categories:
- data warehouse

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

# 数据仓库的基本概念

## 概念

英文名为**Data Warehouse**, 简写为**DW**或**DWH**. 出现的目的是*构建面向分析的集成化数据环境, 为企业提供决策支持*(Decision Support). 因为分析性报告和决策支持目的而出现的技术. 

之所以叫做 **"仓库"** 而不是 **"工厂"**就是因为DW本身是不生产或消费任何数据, 数据来源于外部, 并且开放给外部应用. 

## 基本特征

DW是*面向主题的, 集成的, 非易失的*和*时变的*数据集合, 用以支持决策. 

1. **面向主题**(Subject Oriented)

    在传统数据库中, 最大的特点就是面向应用进行数据的组织, 各个系统可能是相互分离的, 而数据仓库则是面向主题的. 

    * 抽象上来说, 主题就是在较高层次上讲企业信息系统中数据进行综合、归类分析利用. 每一个主题基本对应一个宏观分析领域. 
    * 逻辑上来说, 主题是对应企业中某一个宏观分析领域所涉及的**分析对象**. 

2. **集成的**(Integrate)

    通过对分散、独立、异构的数据库数据进行*抽取、清理、转换*和*汇总*便得到了数据仓库的数据， 这样能保证整个企业的数据的一致性, 避免了产生了**信息孤岛**. 

    数据仓库中的综合数据不能从原有的数据库系统直接得到. 所以在数据进入到DW之前, 必然要经过统一与综合(抽取和清洗), 这就是DW建设中, **最关键**、**最复杂**的一步. 

3. **非易失性**(Non-Volatile)

    非易失性也可称为稳定性或不可更新性. 数据仓库的数据反映的是相当一段时间内的**历史数据的内容**, 是不同时点的数据库的*快照*的集合, 以及基于这些快照进行统计、综合和重组的导出数据. 基于这个特点, DW一般有大量的查询操作, 但修改和删除操作很少. 通常只需要定期的加载和更新. 

4. **时变**(Time Variant)

    数据仓库包含各种粒度的历史数据. 虽然说DW的用户不能修改数据, 但并不是说数据仓库的数据就是永远不变的. 分析的结果只能反映过去的情况, 当业务变化后, 挖掘出的模式会失去时效性. 所以说, DW中的数据需要更新, 以适应决策的需要. 

    1. DW的数据时限一般要远远长于操作型数据的数据时限. 
    2. 操作型系统存储的是当前的数据, 而数据仓库中的数据是历史数据. 
    3. 数据仓库中的数据是按照时间顺序追加的, 它们都带有时间属性. 



# 数据仓库与数据库的区别

>  这两者的区别其实就是`OLAP`与`OLTP`的区别. 



* 联机事务处理**OLTP** (On-Line Transaction Processing), 也可以叫做面向交易的处理系统. 是针对具体业务在数据库联机的日常操作, 通常对少数记录进行查询、修改. 用户较为关心的是响应时间、数据的安全性、完整性和并发支持的用户数等问题. 例如MySQL, Oracle等关系型数据库一般属于OLTP

* 联机分析处理**OLAP**(On-Line Analytical Processing)一般针对某些主题的历史数据进行分析, 支持管理决策. 



通过这两个的对比就能发现, 数据仓库的出现并不是为了替代数据库的. 

数据库设计是尽量避免冗余, 一般是针对某一业务进行设计的. **而数据仓库在设计时有意引入冗余, 依照分析需求、分析维度、分析指标进行设计**. 

> **总的来说数据库是为了捕获数据而设计, 数据存库是为了分析数据而设计**. 



# 数据仓库分层架构

按照数据流入流出的过程, DW架构可分为: **源数据、数据仓库、数据应用**. 



