---
title: "AES初了解"
subtitle: ""
date: 2024-04-23T10:59:07+08:00
draft: false
author:
  name: Mustard	
  link: https://www.buli-home.cn
  email: mustard_gxg@foxmail.com
  avatar: https://cdn.jsdelivr.net/gh/immustard/gallery/Portrait.png
description: ""
keywords: ""
license: ""
comment: false
weight: 0

tags:
- security
- AES
- encrypt
categories:
- security

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

## 简介
**AES 高级加密标准 (Advanced Encryption Standard)** 是最常见的对称加密算法. 
> 对称加密算法也就是加密和解密用相同的密钥. 优点是速度快, 缺点就是如果秘钥丢失, 就容易解密密文, 安全性相对较差. 

AES 的区块长度固定为 `128位`, 密钥长度则可以是 `128bit`, `192bit` 或 `256bit`. 换算成字节长度就是密码必须是 `16字节`, `24字节`, `32字节`. 

## 加密模式
AES 的加密模式有这几种:
* `ECB`: 电码本模式
* `CBC`: 密码分组链接模式
* `CTR`: 计算器模式
* `CFB`: 密码反馈模式
* `OFB`: 输出反馈模式

### EBC
原理就是将整段明文切成若干小段, 然后与密钥进行加密. 

<center>     <img style="border-radius: 0.3125em;     box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"      src="https://cdn.jsdelivr.net/gh/immustard/gallery/pictures/202404231011700.png" width = "65%" alt="" onclick="window.open(this.src)"/>     <br>     <div style="color:orange; border-bottom: 1px solid #d9d9d9;     display: inline-block;     color: #999;     padding: 2px;">       EBC 加/解密   	</div> </center>

### CBC
原理就是将整段明文切成若干小段, 然后每一小段与初始块或上一段的密文进行异或运算后, 再与密钥进行加密. 

<center>     <img style="border-radius: 0.3125em;     box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"      src="https://cdn.jsdelivr.net/gh/immustard/gallery/pictures/202404231000302.png" width = "65%" alt="" onclick="window.open(this.src)"/>     <br>     <div style="color:orange; border-bottom: 1px solid #d9d9d9;     display: inline-block;     color: #999;     padding: 2px;">       CBC 加/解密   	</div> </center>

## 填充
从上面可以看出, AES 在进行加密的时候是将明文拆成一个个的明文块, 每一个明文块长度是 `128bit`. 这里就会发现一个问题, 假如一段明文长度是 `196bit`, 第二个明文块只有 `64bit`, 不足 `128bit`. 这个时候就需要对明文块进行填充 (Padding).

几种典型的填充方式: 
* `NoPadding`: 不做任何填充, 但是要求明文必须是 16字节 的整数倍.
* `PKCS5Padding`(默认): 如果明文块少于 16字节(`128bit`), 在明文块末尾补足相应数量的字符, 且每个字节的值等于缺少的字符数. 🌰 明文: {1,2,3,4,5,a,b,c,d,e}, 缺少 6个字节, 则补全为: {1,2,3,4,5,a,b,c,d,e,6,6,6,6,6,6}.
* `ISO10126Padding`: 如果明文块少于 16字节(`128bit`), 在明文块末尾补足相应数量的字符, 最后一个字符值等于缺少的字符数, 其他字符填充随机数. 🌰 明文: {1,2,3,4,5,a,b,c,d,e}, 缺少 6个字节, 则补全为: {1,2,3,4,5,a,b,c,d,e,G,$,3,9,f,6}.
* `PKCS7Padding`: 与 `PKCS5Padding` 相似, 区别是 `PKCS5Padding` 的 `blocksize` 为 8字节, 而 `PKCS7Padding` 的 `blocksize` 可以为 1到255字节

> ⚠ 注意: 在加密的时候使用了某一种填充方式, 解密的时候也必须采用同样的填充方式. 
