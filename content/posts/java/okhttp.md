---
title: "OkHttp"
subtitle: ""
date: 2022-10-25T18:51:55+08:00
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



> 最近项目里要向其他的地址简单请求(就是不包括文件的上传下载). 之前用的都是`HttpClient`, 发现`HttpClient`的更新优化不是很积极了. 于是打算换一个框架来弄. 也就发现了`OkHttp`. 



## `OkHttp`比`HttpClient`好在哪?

### 首先来看一下使用的区别:

#### `HttpClient`

发送请求主要分为这几个步骤: 

1. 创建`CloseableHttpClient`对象(同步)或者`CloseableHttpAsyncClient`对象(异步)
2. 创建`http`请求对象
3. 调用`execute()`方法执行请求, 如果是异步请求则需在执行前调用`start()`方法



#### `OkHttp`

发送请求主要分为这几个步骤: 

1. 创建`OkHttpClient`对象

2. 创建`Request`对象

3. 将`Request`对象封装为`Call`

4. 通过`Call`来执行同步或异步请求, 调用`execute()`方法同步执行, 调用`enqueue()`方法异步执行

