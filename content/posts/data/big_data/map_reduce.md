---
title: "分布式计算框架--MapReduce"
subtitle: "MapReduce"
date: 2023-02-28T13:26:21+08:00
draft: true
author: ""
authorLink: ""
description: ""
keywords: ""
license: ""
comment: false
weight: 0

tags:
- Hadoop
- Map Reduce
- big data
categories:
- Hadoop
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



## 概述

Hadoop MapReduce 是一个**分布式计算框架**, 用于编写**批处理**应用程序. 编写好的程序可以提交到 Hadoop 集群上用于并行处理大规模的数据集. 

MapReduce 作业通过将输入的数据集拆分为独立的块, 这些块由`map`以**并行的**方式处理, 框架对`map`的输出进行排序, 然后输入到`reduce`中. MapReduce 框架专门用于键值对`<key, value>`处理, 它将作业的输入视为一组`<key, value>`, 并生成一组`<key, value>`作为输出. 

> 输入和输出的`key`和`value`都必须实现[Writable](https://hadoop.apache.org/docs/stable/api/org/apache/hadoop/io/Writable.html)接口. 



## 编程模型简述

现在以词频统计(word count)为🌰, MapReduce 处理的流程:

<center>     <img style="border-radius: 0.3125em;     box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"      src="https://cdn.jsdelivr.net/gh/immustard/gallery/pictures/202302281406873.png" width = "65%" alt="" onclick="window.open(this.src)"/>     <br>     <div style="color:orange; border-bottom: 1px solid #d9d9d9;     display: inline-block;     color: #999;     padding: 2px;">       wc 处理流程   	</div> </center>

1. **input**: 读取文本文件
2. **splitting**: 将文本按照行进行拆分, 此时得到`K1`行数, `V1`表示对应行的文本内容
3. **mapping**: **并行**将每一行按照空格进行拆分, 拆分得到的`List(K2,V2)`, 其中`K2`代表每个单词, 由于是词频统计, 所以`V2`的值为 1, 代表出现 1 次
4. **shuffling**: 由于`Mapping`操作可能是在不同机器上处理的, 所以需要通过`shuffling`将形同的`key`的数据发送到同一个节点上进行合并, 这样才能统计出最终的结果, 此时得到的`K2`为每个单词, `List(V2)`为可迭代集合, `V2`就是`Mapping`中的`V2`
5. **reducing**: 这个案例中是统计词频, 所以`reducing`对`List(V2)`进行求和操作, 然后输出



> MapReduce 编程模型中`splitting`和`shuffling`操作都是有框架实现的, 需要自己实现的只有`mapping`和`reducing`. 
