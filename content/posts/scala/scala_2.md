---
title: "Scala变量和数据类型"
subtitle: ""
date: 2022-07-07T09:00:03+08:00
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
- Scala
categories:
- Scala

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



## 注释

Scala 的注释和 Java 的完全一样:

```java
// 1. 单行注释

/* 2. 多行注释 */

/**
 * 3. 文档注释
 */
```



## 变量和常量



### 基本语法

```scala
// var 变量名 [: 变量类型] = 初始值
var i: Int = 10
// val 常量名 [: 常量类型] = 初始值
val j: Int = 20
```

> * 能用常量的地方就不要用变量

> 1. 声明变(常)量时, 类型可以省略, 编译器自动推导, 即**类型推导**
> 2. 类型确定后, 就不能更改, 因为 Scala 是强数据类型语音
> 3. 变量声明时, 必须有初始值



## 标识符的命名规范



### 命名规范

Scala 中的标识符声明, **基本和Java是一致的**, 但是细节上会有变化: 

1. 以字母或下划线开头, 后接字母、数字、下划线
2. **以操作符开头, 且只包含操作符(+-*/#!等)**
3. **用反引号\`...\`包括的任意字符串, 即使是 Scala 关键字(39个)也可以**
   * package, import, class, **object**, **trait**, extends, **with**, type, forSom
   * private, protected, abstract, **sealed**, final, **implicit**, lazy, override
   * try, catch, finally, throw
   * if, else, **match**, case, do, while, for, return, **yield**
   * **def**, **var**, **val**
   * this, super
   * new
   * true, false, null



> 看几个特殊一点的🌰
>
> ```scala
> object Hello {
> 
>   def main(args: Array[String]): Unit = {
>     // ok 因为在 Scala 中 Int 是预定义的字符, 不是关键字, 但是不推荐
>     var Int: String = ""
>     // ok 单独一个下划线不可作为标识符, 因为 _ 被认为是一个方法
>     var _: String = "str"
>     // 会报错
>     println(_)
> 
>     // ok
>     var -+*/#! : String = ""
>     // error 以操作符开否, 必都是操作符
>     var -_*/#!1 : String = ""
>       
>     // error
>     var if: String = ""
>     // ok
>     var `if`: String = ""
>   }
>   
> }
> ```



## 字符串输出



### 基本语法

1. 字符串, 通过`+`号连接
2. `printf`用法: 字符串，通过`%`传值。
3. 字符串模板(插值字符串): 通过`$`获取变量值

> 来看看🌰:
>
> ```scala
> object Hello {
> 
>   def main(args: Array[String]): Unit = {
>     val name = "mustard"
>     val age = 18
> 
>     printf("name=%s, age=%d", name, age)
> 
>     /**
>      * 多行字符串， 在 Scala 中，利用三个双引号包围多行字符串就可以实现。
>      * 输入的内容，带有空格、 \t 之类，导致每一行的开始位置不能整洁对齐。
>      * 应用 scala 的 stripMargin 方法，在 scala 中 stripMargin 默认是 "|" 作为连接符，
>      * 在多行换行的行头前面加一个 "|" 符号即可。
>      */
>     val sql1 =
>       """
>         |select
>         |   name
>         |   age
>         |from user
>         |where name = "mustard"
>       """.stripMargin
>     println(sql1)
> 
>     // 如果需要对变量进行运算，那么可以加 ${}
>     val sql2 =
>       s"""
>          |select
>          |   name
>          |   age
>          |from user
>          |where name = "$name" and age = ${age + 2}
>       """.stripMargin
>     println(sql2)
> 
>     val s = s"name=$name"
>     println(s)
>   }
> 
> }
> ```
>
> 



## 数据类型



### Java数据类型

先来回顾一下 Java 的数据类型: 

1. 基本数据类型(8种): `char`, `byte`, `short`, `int`, `long`, `float`, `double`, `bollean`
2. 引用类型: (对象类型)



**由于 Java 有基本类型, 并且基本类型并不是真正意义的对象, 所以 Java 语言并不是真正意义的面向对象**. 

> 注意哈: Java 中基本类型和引用类型没有共同的祖先



### Scala数据类型

1. Scala 中一切都是对象, **都是`Any`的子类**
2. Scala 中数据类型分为两大类: 数值类型 (`AnyVal`), 引用类型 (`AnyRef`), **但是都是对象**
3. Scala 数据类型仍然遵守**隐式转换** (低精度的值向高精度的值自动转换)
4. Scala 中的`StringOps`是对 Java 中的`String`增强
5. `Unit`: 对应 Java 中的`void`, 用于方法返回值的位置, 表示方法没有返回值. `Unit`是一个数据类型, 只有一个对象就是`()`. `Void`不是数据类型, 只是一个关键字. 
6. `Null`是一个类型, 只有一个对象就是`null`. **它是所有引用类型(`AnyRef`)的子类**. 
7. `Nothing`: **是所有数据类型的子类**, 主要用在一个函数没有明确返回值时使用, 因为这样可以把抛出的返回值, 返回给任何的变量或者函数. 



#### 整数类型

| 数据类型   | 描述                                                    |
| ---------- | ------------------------------------------------------- |
| `Byte[1]`  | 8位有符号补码整数. 数值区间: -128 到 127                |
| `Short[2]` | 16位有符号补码整数. 数值区间: -32768 到 32767           |
| `Int[4]`   | 32位有符号补码整数. 数值区间: -2147483648 到 2147483647 |
| `Long[8]`  | 64位有符号补码整数. 数值区间: -2^64 到 2^64-1           |

> Scala 的整型，默认为`Int`型，声明`Long`型，须后加`l`或`L`



#### 浮点类型

| 数据类型    | 描述 |
| ----------- | ---- |
| `FLoat[4]`  | 32位 |
| `Double[8]` | 64位 |

```scala
object TestDataType {
    def main(args: Array[String]): Unit = {
        // 这是个 Float
        var n1 = 1.23456789f
        // 这是个 Double
        var n2 = 1.23456789
    }
}
```



#### 字符类型 (Char)



#### 布尔类型 (Boolean)

占 1 个字节



#### Unit 类型、Null 类型和 Nothing 类型

| 数据类型  | 描述                                                         |
| --------- | ------------------------------------------------------------ |
| `Unit`    | 表示无值, 和其他语言中`void`等同. 用作不返回任何结果的方法的结果类型. `Unit`只有一个实例: `()`, 且没有实际意义 |
| `Null`    | `null`, `Null`类型只有一个实例: `null`. **`Null`可以赋值给任意引用类型(AnyRef), 但是不能赋值给值类型(AnyVal)** |
| `Nothing` | `Nothing`类型在 Scala 的类层级最低端; 它是任何其他类型的子类型. 当一个函数, 确定没有正常的返回值时, 可以用`Nothing`来指定返回类型. |

> `Nothing`的这种机制有一个好处: 可以把返回的值(异常)赋给其它的函数或者变量(兼容性)

```scala
object TestDataType {
    def main(args: Array[String]): Unit = {
        var cat = new Cat()
        // 正确
        cat = null
        
        // 错误
        var n1: Int = null
        
        def test(): Nothing = {
            throw new Exception()
        }
        
        test
    }
}
```



#### 类型转换

* 数值类型自动转换: 精度小的类型自动转换为精度大的数值类型. (隐式转换)

  `Byte < Short < Int < Long < Float < Double`

  1. 自动提升原则: 有多种类型的数据混合运算时, 首先**自动将所有数据转换成精度大的数据类型**, 然后再计算
  2. **把精度大的数值类型赋值给精度小的数值类型时, 会报错**
  3. (`byte`, `short`)和`char`之间不会相互自动转换
  4. `byte`,`short`,`char`三者可以计算, **但会先转换为`int`类型**

   	

* 强制类型转换

  可能造成精度降低或溢出, 所以需要特别注意

  ```scala
  var num: Int = 2.7.toInt
  ```

* 数值类型和`String`类型间的转换

  * 数值 -> `String`: `+ ""`就行, 和 Java 一样
  * `String` -> 数值: `s1.toInt`, `s1.toFloat`, `s1.toDouble`, `s1.toByte`, `s1.toLong`, `s1.toShort`
