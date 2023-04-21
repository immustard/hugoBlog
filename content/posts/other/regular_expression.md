---
title: "正则表达式"
subtitle: "Regular Expression"
date: 2023-01-03T13:50:53+08:00
draft: true
author: ""
authorLink: ""
description: ""
keywords: ""
license: ""
comment: false
weight: 0

tags:
- Regular Expression
categories:
- Regular Expression

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



正则表达式的应用非常广泛, 字符串匹配和替换啊, 表单的规则验证等等. 但是一直都是用什么的时候就查什么, 也没有规范的学习过这个概念. 所以今天我好好的梳理一下这部分知识. 

> 首先吐槽一下, 一开始是谁翻译的叫做'正则表达式', 直接就叫'规则表达式'岂不是更好! 😡





## 通配符

在说正则表达式之前先说一下通配符的概念, 每个人或多或少都接触过通配符: `?`和`*`. 没错就是这俩. 在模糊搜索文件或者文本的时候, 相信都接触过这俩个通配符. `?`匹配0个或1个字符, 而`*`匹配0个或多个字符. 

尽管这种方法很有用, 但是就只有两个的话, 不难看出通配符的作用也是有限的. 所以就出现了更加强大的, 更加灵活的正则表达式. 



## 正则表达式

> 网上关于正则表达式的文章一搜一大堆, 所以这里就不赘述了. 这里主要归纳正则表达式的语法, 以及对应的🌰. 



### 普通字符

| 字符(宽一点宽一点宽一点) | 描述                                                         | 🌰                                                            |
| ------------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| `[ABC]`                  | 匹配`[...]`中的所有字符                                      | <img src="https://cdn.jsdelivr.net/gh/immustard/gallery/pictures/202301031455375.png" style="zoom:50%;" /> |
| `[^ABC]`                 | 匹配除了`[...]`中字符的所有字符                              | <img src="https://cdn.jsdelivr.net/gh/immustard/gallery/pictures/202301031456093.png" style="zoom:50%;" /> |
| `[A-Z]`                  | `[A-Z]`表示一个区间, 匹配所有大写字母, `[a-z]`表示所有小写字母 | <img src="https://cdn.jsdelivr.net/gh/immustard/gallery/pictures/202301031457525.png" style="zoom:50%;" /> |
| `.`                      | 匹配除换行符(`\n`,`\r`)之外的任何单个字符, 相等于`[^\n\r]`   | <img src="https://cdn.jsdelivr.net/gh/immustard/gallery/pictures/202301031458602.png" style="zoom:50%;" /> |
| `[\s\S]`                 | 匹配所有. `\s`是匹配所有空白符(包括换行), `\S`非空白符(不包括换行) | <img src="https://cdn.jsdelivr.net/gh/immustard/gallery/pictures/202301031458960.png" style="zoom:50%;" /> |
| `\w`                     | 匹配字母, 数字, 下划线. 等价于`[A-Za-z0-9_]`                 | <img src="https://cdn.jsdelivr.net/gh/immustard/gallery/pictures/202301031453610.png" style="zoom:50%;" /> |



### 非打印字符

| 字符(宽一点) |                                                              |
| ------------ | ------------------------------------------------------------ |
| `\cx`        | 匹配由`x`指明的控制字符. `x`的值必须为`A-Z`或`a-z`之一. 否则, 将`c`视为一个原义的'c'字符. |
| `\f`         | 匹配一个换页符. 等价于`\x0c`和`\cL`.                         |
| `\n`         | 匹配一个换行符. 等价于`\x0a`和`\cJ`.                         |
| `\r`         | 匹配一个换行符. 等价于`\x0d`和`\cM`.                         |
| `\s`         | 匹配任何空白字符, 包括空格, 制表符, 换页符等等. 等价于`[\f\n\r\t\v]`. (注意Unicode正则表达式会匹配全角空格符) |
| `\S`         | 匹配任何非空白字符. 等价于`[^\f\n\r\t\v]`.                   |
| `\t`         | 匹配一个换行符. 等价于`\x09`和`\cI`.                         |
| `\v`         | 匹配一个换行符. 等价于`\x0b`和`\cK`.                         |

