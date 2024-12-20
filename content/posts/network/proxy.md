---
title: 正向代理和反向代理
subtitle: Forward and Reverse Proxy
date: 2023-08-15T09:43:55+08:00
draft: false
author:
  name: Mustard	
  link: https://www.buli-home.cn
  email: mustard_gxg@foxmail.com
  avatar: https://pub-7360a7072ee341a58e1e9b6541edca66.r2.dev/portrait/mustard.png
description:
keywords:
license:
comment: false
weight: 0
tags:
  - network
  - proxy
categories:
  - network
hiddenFromHomePage: false
hiddenFromSearch: false
summary:
resources:
  - name: featured-image
    src: featured-image.jpg
  - name: featured-image-preview
    src: featured-image-preview.jpg
toc: true
math: false
lightgallery: false
password:
message:
repost:
  enable: false
  url:

# See details front matter: https://fixit.lruihao.cn/documentation/content-management/introduction/#front-matter
---

<!--more-->



> 最近和胖胖讨论的时候发现对于正向代理和反向代理的概念有点模糊. 所以整理做下梳理. 



## 代理
代理就像是中介, 本来是 A  和 B 之间直接连接的, 但是此时添加了一个 C 在中间, A 和 B 现在需要通过 C 作为中介进行连接. 比如说是房产中介, 房东和租客就是通过中介来进行沟通的. 



## 正向代理
正向代理是由客户端发起请求, 但是请求并不直接发送给目标服务器, 而是先发送给代理服务器, 由代理服务器代替客户端发送请求, 并将响应返回给客户端. 

其实正向代理就是顺着请求的方向进行代理. 

举个🌰: 想要访问油管, 但是直接访问是访问不到的. 这个时候就需要一个代理服务器, 我们去访问代理服务器, 然后代理服务器将我们的请求发送个油管, 然后再将油管的响应返回给我们. 

### 作用
1. 访问原来无法访问的资源
2. 缓存
3. 对客户端访问授权, 进行认证
4. 记录用户访问记录, 对外隐藏用户信息



## 反向代理
反向代理可以隐藏服务器的真实 IP, 同时可以将客户端的请求转发到多个服务器中的一个或多个, 用来提高网站的访问速度和安全性. 

### 作用
1. 保证内网安全, 组织 web 攻击
2. 负载均衡



## 区别与联系
正向代理和反向代理的区别在于代理的对象不同: 
* 正向代理是代理客户端, 代理服务器代替客户端向目标服务器发送请求, 目标服务器不知道真正的请求方是谁. 
* 反向代理是代理服务端, 代理服务器代替目标服务器向客户端发送响应, 客户端不知道真正的响应方是谁. 
