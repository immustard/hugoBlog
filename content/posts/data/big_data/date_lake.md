---
title: "数据湖"
subtitle: "Data Lake"
date: 2022-06-15T16:19:40+08:00
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
- big data
- data lake
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



## 什么是数据湖



看了网上很多的资料, 关于数据湖的定义有很多, 我们先来看看AWS([传送门](https://aws.amazon.com/cn/big-data/datalakes-and-analytics/what-is-a-data-lake/))的定义: 

* 数据湖是一个集中式存储库，允许您以任意规模存储所有结构化和非结构化数据。您可以按原样存储数据（无需先对数据进行结构化处理），并运行不同类型的分析 – 从控制面板和可视化到大数据处理、实时分析和机器学习，以指导做出更好的决策。

再来看一下Wikipedia([传送门](https://zh.wikipedia.org/wiki/%E6%95%B0%E6%8D%AE%E6%B9%96))的定义:

* 指使用大型二进制对象或文件这样的自然格式储存数据的系统 。它通常把所有的企业数据统一存储，既包括源系统中的原始副本，也包括转换后的数据，比如那些用于报表, 可视化, 数据分析和机器学习的数据。数据湖可以包括关系数据库的结构化数据(行与列)、半结构化的数据(CSV，日志，XML, JSON)，非结构化数据 (电子邮件、文件、PDF)和 二进制数据(图像、音频、视频)。



比较统一的一点是数据湖存储的是未经加工的原始数据, 包含结构化(如关系型数据库中的表)、半结构化(如CSV、日志、XML、JSON)和非结构化(如电子邮件、文档、PDF)的各类数据. 因为是原始数据, 所以也就保持着数据在业务系统中原来的样子. 这就是使得数据湖一定要具备完善的管理能力, 也就是要有完善的元数据, 可以管理各类数据相关的要素，包括数据源、数据格式、连接信息、数据schema、权限管理等. 



因为数据湖是为了分析数据而演化来的, 也就要存储各类分析处理的中间结果, 并且还要记录完整的分析过程. 



> 数据沼泽是一个劣化的数据湖, 用户无法访问, 或是没什么价值
