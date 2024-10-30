---
title: "Scala运算符"
subtitle: ""
date: 2022-07-14T20:38:28+08:00
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



> Scala 运算符的使用和 Java 的基本相同, 只有个别细节上不同



## 算数运算符

| 运算符 | 运算       | 🌰              | 结果      |
| ------ | ---------- | -------------- | --------- |
| `+`    | 正号       | `+3`           | 3         |
| `-`    | 负号       | `b=4;-b`       | -4        |
| `+`    | 加         | `5+5`          | 10        |
| `-`    | 减         | `6-4`          | 2         |
| `*`    | 乘         | `3*4`          | 12        |
| `/`    | 除         | `5/5`          | 1         |
| `%`    | 取模(取余) | `7%5`          | 2         |
| `+`    | 字符串相加 | `"Must"+"ard"` | "Mustard" |



## 关系运算符(比较运算符)

| 运算符 | 运算     | 🌰      | 结果  |
| ------ | -------- | ------ | ----- |
| `==`   | 相等于   | `4==3` | false |
| `!=`   | 不等于   | `4!=3` | true  |
| `<`    | 小于     | `4<3`  | false |
| `>`    | 大于     | `4>3`  | true  |
| `<=`   | 小于等于 | `4<=3` | false |
| `>=`   | 大于等于 | `4>=3` | true  |

>Java 和 Scala 中关于`==`的区别
>
>* Java: 
>
>  `==`比较两个变量本身的值, 即两个对象在内存中的首地址
>
>  `equals`比较字符串中包含的内容是否相同
>
>* Scala: 
>
>  `==`更类似于 Java 中的`equals`
>
>  ```scala
>  def main(args: Array[String]): Unit = {
>    val s1 = "abc"
>    val s2 = new String("abc")
>    
>    // true
>    println(s1 == s2)
>    // false
>    println(s1.eq(s2))
>  }
>  ```



## 逻辑运算符

| 运算符 | 描述   | 实例 (A=true, B=false) |
| ------ | ------ | ---------------------- |
| `&&`   | 逻辑与 | `A && B = false`       |
| `||`   | 逻辑或 | `A || B = true`        |
| `!`    | 逻辑非 | `!(A && B) = true`     |



## 位运算符

| 运算符 | 描述       | 实例 (a=60, b=13) => (a: 0011 1100, b: 0000 1101) |
| ------ | ---------- | ------------------------------------------------- |
| `&`    | 按位与     | `a & b = 12` => 0000 1100                         |
| `|`    | 按位或     | `a | b = 61` => 0011 1101                         |
| `^`    | 按位异或   | `a ^ b = 49` => 0011 0001                         |
| `~`    | 按位取反   | `~a = -61` => 1100 0011                           |
| `<<`   | 左移       | `a << 2 = 240` => 0011 0000                       |
| `>>`   | 右移       | `a >> 2 = 15` => 0000 1111                        |
| `>>>`  | 无符号右移 | `a >>> 2 = 15` => 0000 1111                       |



## 赋值运算符

Scala中的赋值运算符就是 `算数运算符/位运算符`+`=`

> Scala 中没有`++`, `--`操作, 这点`Swift`和这里是相同的, 都是因为觉得这两个运算符并不符合面向对象的思想. 需要通过`+=`和`-=`来实现. 



## Scala 运算符的本质

看源码可以知道, Scala 中其实是没有运算符的, 所有的运算符都是方法

```Scala
object TestOpt {
  def main(args: Array[String]): Unit = {
    // 标准的加法运算
    val i: Int = 1.+(1)
    
    // 1. 当调用对象的方法时, `.`可以省略
    val j: Int = 1 + (1)
    
    // 2. 如果函数参数只有一个, 或者没有参数, `()`可以省略
    val k: Int = 1 + 1
    
    println(1.toString())
    println(1 toString())
    println(1 toString)
  }
}
```

