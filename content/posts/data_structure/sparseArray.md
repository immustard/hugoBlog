---
title: "稀疏数组"
subtitle: "Sparse Array"
date: 2022-03-07T16:57:32+08:00
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
- data structure
- Java
categories:
- data structure

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



首先, 看一个. 现在有一个五子棋程序, 其中有一个**存盘退出**和**续上盘**的功能. 

{{< html >}}<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="https://webp.buli-home.cn/2022/03/202203071712035.png" width = "65%" alt="" onclick="window.open(this.src)"/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      二维数组记录棋盘
  	</div>
</center>{{< /html >}}





从上面这张图就能看到, 很多值就是默认值(`0`), 也就是说记录了很多没有意义的值, 所以就引出了**稀疏数组**. 



## 基本介绍

当一个数组中大部分元素都是同一个值的数组时, 可以使用**稀疏数组**来保存该数组. 

稀疏数组的处理方法是: 

1. 记录数组一共几行几列, 有多少个不同的值
2. 把有不同值的元素的行列及值记录在一个小规模的数组中, 从而所辖程序的规模

```java
// 一个正常的二维数组, 一共有7行8列, 其中大部分都是0
int[][] array = {
  {0,1,0,0,0,0,0,0},
  {0,0,0,2,0,0,0,0},
  {0,0,0,0,0,0,3,0},
  {0,-4,0,0,0,-5,0,0},
  {0,0,0,0,0,0,0,0},
  {0,0,0,0,-6,0,0,0},
  {0,0,7,0,0,0,0,0},
}
```

| 行(row) | 列(column) | 值(value) |
| :-----: | :--------: | :-------: |
|    7    |     8      |     7     |
|    0    |     1      |     1     |
|    1    |     3      |     2     |
|    2    |     6      |     3     |
|    3    |     1      |    -4     |
|    3    |     5      |    -5     |
|    5    |     4      |    -6     |
|    6    |     2      |     7     |

> 表中第一行记录了一共几行几列以及多少个非零值

从上面这个就能看出来, 将原本需要`7*8=56`个空间变为了`3*8=24`个空间. 



## 实现思路

1. **二维数组** 转 **稀疏数组**

   1. 遍历原始的二维数组, 得到有效数据的个数`sum`

   2. 根据`sum`就可以创建稀疏数组`int[sum+1][3]`

   3. 将二维数组的有效数据存入到稀疏数组

2. **稀疏数组** 转 **二维数组**
   1. 先读取稀疏数组第一行, 根据第一行的数组创建原始二维数组
   2. 在读取稀疏数组后面的数据, 并赋值给二维数组
