---
title: "Scala概述"
subtitle: ""
date: 2022-07-06T16:30:20+08:00
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
- scala
categories:
- scala

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





> Scala这门语言是怎么发展过来的, 网上有很多资料, 这里就不赘述了. 

## Scala和Java的关系

一般来说, 学习 Scala 之前都会或多或少的接触过 Java, 而 Scala 是基于 Java 的, 因此在学习 Scala 之前, 要先弄清楚 Java, Scala 和 JVM 的关系是很有用的. 

{{< html >}}<center>     <img style="border-radius: 0.3125em;     box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"      src="https://webp.buli-home.cn/2022/07/202207061620363.png" width = "65%" alt="" onclick="window.open(this.src)"/>     <br>     <div style="color:orange; border-bottom: 1px solid #d9d9d9;     display: inline-block;     color: #999;     padding: 2px;">       Java, Scala和JVM的关系   	</div> </center>{{< /html >}}



## 配置Scala环境



> 以 Windows 为例 

1. 首先确保 JDK1.8 安装成功

2. 下载对应的 Scala 安装文件: [传送门](https://www.scala-lang.org/download/) (我下载的是`zip`)

3. 解压 zip 

4. 配置 Scala 的环境变量

   {{< html >}}<center>     <img style="border-radius: 0.3125em;     box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"      src="https://webp.buli-home.cn/2022/07/202207070905544.png" width = "65%" alt="" onclick="window.open(this.src)"/>     <br>     <div style="color:orange; border-bottom: 1px solid #d9d9d9;     display: inline-block;     color: #999;     padding: 2px;">       配置 SCALA_HOME   	</div> </center>{{< /html >}}

   {{< html >}}<center>     <img style="border-radius: 0.3125em;     box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"      src="https://webp.buli-home.cn/2022/07/202207070907001.png" width = "65%" alt="" onclick="window.open(this.src)"/>     <br>     <div style="color:orange; border-bottom: 1px solid #d9d9d9;     display: inline-block;     color: #999;     padding: 2px;">       在 Path 中添加   	</div> </center>{{< /html >}}





## 配置IDEA



1. 安装插件 Scala
2. 创建项目之后, 右键项目目录 -> Add Framework Support -> 选择 Scala
