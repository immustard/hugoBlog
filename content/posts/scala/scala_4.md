---
title: "Scala控制流程"
subtitle: ""
date: 2022-07-15T15:51:27+08:00
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



## 分支控制



### `if-else`

1. 单分支: `if{}`
2. 双分支: `if{} else{}`
3. 多分支: `if{} else if{}`



> * Scala 中的`if-else`是**有返回值的**, 具体取决于**满足条件的代码块的最后一行内容**
> * Scala 中是没有三元运算符的, 但是可以用`if-else`代替



### `switch`

Scala 中是没有`switch`的, 而是使用**模式匹配**来处理的



## 循环控制



### `for`

Scala 中为`for`提供了很多的特性: 



#### 1. 范围数据循环 (`to`)

```scala
// 前后闭合: [1,3]
for (i <- 1 to 3) {
    println(i)
}
```



#### 2. 范围数据循环 (`until`)

```scala
// 前闭后开: [1, 3)
for (i <- 1 until 3) {
    println(i)
}
```



#### 3. 循环守卫

```scala
for (i <- 1 to 3 if i != 2) {
  println(i)
}

// 上面的代码就相当于:
for (i <- 1 to 3) {
    if (i != 2) {
        println(i)
    }
}
```



#### 4. 循环步长

```scala
for (i <- 1 to 10 by 2) {
    println(i)
}
```



#### 5. 嵌套循环

```scala
// 因为没有关键字, 所以一定要加 `;` 来进行分割
for (i <- 1 to 3; j <- 1 to 3) {
    println("i=" + i + ", j=" + j)
}

// 上面的代码相当于:
for (i <- 1 to 3) {
    for (j <- 1 to 3) {
        println("i=" + i + ", j=" + j)
    }
}
```



#### 6. 引入变量

```scala
for (i <- 1 to 3; j = 4 - i) {
    println("i=" + i + ", j=" + j)
}

// for 推导式有一个不成文的规定: 
// 1. 仅包含单一表达式时, 使用圆括号
// 2. 当包含多个表达式时, 一般每一行一个表达式, 并且用花括号
for {
    i <- 1 to 3
    j = 4 - i
} {
    println("i=" + i + ", j=" + j)	
}
```



#### 7. 循环返回值

```scala
val res = for (i <- 1 to 5) yield { i * 2 }
// 输出 2, 4, 6, 8, 10
println(res)
```



#### 8. 倒序

```scala
for (i <- 1 to 10 reverse) {
    println(i)
}
```



### `while`和`do..while`

Scala 中的`while`和`do..while`和 Java 中的用法一致



#### `while`

1. 循环条件是返回一个布尔值的表达式
2. `while`先判断再执行
3. 与`for`不同, **`while`没有返回值**, 即整个`while`语句的**结果是`Unit`类型** 



#### `do..while`

1. 循环条件是返回一个布尔值的表达式
2. `do..while`先执行再判断



## 循环中断

Scala 内置控制结构特地**去掉了`break`和`continue`**. 是因为更好的适应**函数式编程**, 推荐使用函数式的风格解决`break`和`continue`, 而不是一个关键字. Scala 中使用`breakable`控制结构来实现`break`和`continue`功能. 



### 异常的方式退出

```scala
def main(args: Array[String]): Unit = {
    try {
        for (elem <- 1 to 10) {
            println(elem)
            if (elem == 5) throw new RuntimeException
        }
    } catch {
        case e =>
    }
    println("结束循环")
}
```



### Scala 自带函数退出

```scala
import scala.util.control.Breaks

def main(args: Array[String]): Unit = {
    Breaks.breakable(
        for (ele <- 1 to 10) {
            println(ele)
            if (ele == 5) Breaks.break()
        }
    )

    println("结束循环")
}
```



### 对`break`进行省略

```scala
import scala.util.control.Breaks._

def main(args: Array[String]): Unit = {
    breakable(
        for (ele <- 1 to 10) {
            println(ele)
            if (ele == 5) break
        }
    )

    println("结束循环")
}
```



### `continue`

```scala
import scala.util.control.Breaks._

def main(args: Array[String]): Unit = {
    for (ele <- 1 to 10) {
        breakable(
            if (ele % 2 == 1)
            	break
            else
            	println(ele)
        )
    }

    println("结束循环")
}
```

这里的`breakable`和上面的区别是将其放入到了循环内部, 这样可以实现结束本次执行而不是整个循环结束, 从而实现`continue`的功能. 
