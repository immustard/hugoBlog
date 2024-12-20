---
title: "系统调用、异常和中断"
subtitle: "System Call, Exception, Interrupt"
date: 2022-03-16T13:44:12+08:00
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



## 系统调用, 异常, 中断的特点

### 来源

1. **系统调用**: 应用程序
2. **异常**: 应用程序
3. **中断**: 外设



### 处理时间

1. **系统调用**: 异步或同步
2. **异常**: 同步
3. **中断**: 异步



### 响应

1. **系统调用**: 等待和持续
2. **异常**: 杀死或重新执行意想不到的应用程序指令
3. **中断**: 持续, 对用户应用程序是透明的



---

## 中断和异常的处理机制

* 中断陈外设的事件
* 异常是内部CPU的事件
* 中断和异常迫使CPU访问一些被中断和异常服务访问的功能



### 中断

#### 硬件

* 设置中断标记**[CPU初始化]**
  1. 将内部、外部事件设置中断标记
  2. 中断时间的ID



#### 软件

* 保存当前处理状态: 为了确保之后能从打断的地方能够继续执行
* 中断服务程序处理
* 清除中断标记
* 恢复之前保存的处理状态



### 异常

* 保存现场
* 异常处理
  * 杀死产生异常的程序
  * 重新执行异常指令 (这种情况下, 对于用户来说, 异常就是透明的)
* 恢复现场



---

## 系统调用

系统调用和前面的中断和异常从名字上看就不一样(认真脸). 应用程序需要系统提供一些服务, 而这些服务不能由应用程序直接执行, 需要由操作系统来执行, 这个过程就需要接口, 这个接口就称为系统调用接口. 

举个🌰, **C语言**中的`printf()`就会触发系统调用`write()`. 



* 程序访问主要是通过**高层次**的API接口, 而不是直接进行系统调用. 
  * Win32 API 用于 Windows
  * POSIX API 用于 POSIX-based systems (包括UNIX, Linux, Mac OS 的所有版本)
  * Java API 用于 JVM
* 通常, 每个系统调用相关的序号. 系统调用接口根据这些需要来维护表的索引.
* 系统调用接口调用内核态中预期的系统调用, 并返回系统调用的状态和其他任何返回值
* 用户不需要知道系统调用是如何实现的, 只需要获取API和了解操作系统将什么作为返回结果. 操作系统接口的细节大部分都隐藏在API中, 通过运行程序支持的库来管理(包括编译器的库来创建函数集). 

* **系统调用**和传统的**函数调用**是有区别的:

  * 当应用程序发出**函数调用**的时候, 是在一个栈空间完成了参数的传递和参数的返回. 
  * **系统调用**的执行过程中, 应用程序和OS是有各自的堆栈. 当应用程序发出系统调用的时候, 当切换到内核中执行之后也需要切换堆栈. 同时, **也需要完成特权级的转换(从用户态转为内核态)**. 

  也就是说**系统调用**的开销是比**函数调用**的开销大得多, 但是这样的做法也提高了安全性. 



### 跨越操作系统边界的开销

> 通过上面可以了解到, 其实系统调用, 异常和中断就是操作系统和应用程序, 以及操作系统和外设之间跨越了边界. 

* 在执行时间上的开销超过程序调用
* 开销: 
  * 建立中断/异常/系统调用号与对应服务例程映射关系的初始化开销
  * 建立内核堆栈
  * 验证参数, 因为操作系统是不信任应用程序的
  * 内核态映射到用户态的地址空间, 更新页面映射权限. 这是一个拷贝的过程, 因为不能将内核态中的数据简单的用传递指针的方式传递给用户态. 
  * 内核态独立地址空间, TLB
