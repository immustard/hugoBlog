---
title: "内存中的地址空间"
subtitle: "Address Space"
date: 2022-03-17T11:02:07+08:00
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
- operating system`

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



## 地址空间

之前已经说过了, 地址空间分为两种: 

1. **物理地址空间**: 硬件支持的地址空间

   这部分地址空间的管理和控制是由硬件来完成的. 

2. **逻辑地址空间**: 一个运行的程序所拥有的内存范围

   相对于物理地址空间而言, 程序所能"看到的"逻辑地址空间更简单一点, 就是一个一维的线性的地址空间. 

但是最终, 逻辑地址和物理地址都是需要对应上的, 这部分就是由OS来完成的.   



## 地址生成

用一个C语言的函数来举个🌰

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="https://cdn.jsdelivr.net/gh/immustard/gallery/pictures/2022-03/202203171133969.png" width = "65%" alt="" onclick="window.open(this.src)"/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      逻辑地址生成
  	</div>   
</center>


从最开始的**'符号逻辑地址'**到最终的**'具体逻辑地址'**, 经过了上面的这些转换过程, 而这些过程是基本上不需要操作系统来帮助的, 而是通过应用程序、编译器或者Loader就可以完成.  但是当把这个地址放入到内存中之后也是逻辑地址而不是物理地址. 



再把之前的操作系统架构中的图片拿出来:

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="https://cdn.jsdelivr.net/gh/immustard/gallery/pictures/2022-04/202204271432111.png" width = "65%" alt="" onclick="window.open(this.src)"/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      物理地址生成
  	</div>
</center>


* CPU方面: 

  1. 运算器需要在逻辑地址的内存内容
  2. 内存管理单元(MMU)寻找在逻辑地址和物理地址之间的映射
  3. 控制器从总线发送在物理地址的内存内容的请求

* 内存方面:

  4. 内存发送物理地址内存的内容给CPU

* 操作系统方面:

  建立逻辑地址和物理地址之间的映射



>  **操作系统的一个重要的作用就是: 确保放在内存中的程序相互之间不能相互干扰, 每个程序去访问地址空间是合法的. 换而言之, 确保每个程序访问地址空间是在一个范围之内的.  **
