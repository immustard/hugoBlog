---
title: "计算机体系结构及内存分层体系"
subtitle: "Computer Architecture & Memory Hierarchy"
date: 2022-03-16T14:22:17+08:00
draft: false
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
- operating system
categories:
- operating system

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



## 计算机基本硬件结构

* CPU主要是完成对程序的控制
* 内存主要放置程序的代码和处理的数据
* 设备

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="http://mustard_gxg.gitee.io/pic/pictures/2022-03/202203161441911.png" width = "65%" alt="" onclick="window.open(this.src)"/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      计算机基本硬件结构
  	</div>
</center>

## 内存分层体系

放上这张图(这张图应该很多人都看到过)

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="https://cdn.jsdelivr.net/gh/immustard/gallery/pictures/2022-03/202203171043586.png" width = "65%" alt="" onclick="window.open(this.src)"/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      内存延时🌰
  	</div>
</center>




### 操作系统需要完成的四个目标

1. **抽象**: 逻辑地址空间

   我们希望应用程序在内存中运行的时候, 不用考虑很多细节(物理内存地址在什么地方, 外设在什么地方), 只需要访问一个连续的地址空间就可以了, 即**逻辑地址空间**. 

2. **保护**: 独立地址空间

   因为在内存中运行着多个不同的应用程序. 运行的过程中, 有可能会访问其他应用程序的地址空间并造成破坏, 所以就需要将不同应用程序的运行空间进行隔离.

3. **共享**: 访问相同内存

   不同的程序之间除了隔离之外, 还会有交互. 所以操作系统提供共享的内存空间来完成. 

4. **虚拟化**: 更多的地址空间

   当内存中的程序过多的时候, 有可能会出现内存不够的情况. 这样, 就会把急需要地址空间的程序放在内存中, 暂时不需要地址空间的程序先放在硬盘上去.  



### 操作系统中管理内存的不同方法

1. 程序重定位
2. 分段
3. 分页
4. 虚拟内存
5. 按需分页虚拟内存



### 实现高度依赖于硬件

* 必须知道内存架构
* MMU(内存管理单元): 硬件组件负责处理CPU的内存访问请求
