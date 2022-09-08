---
title: "Scala函数式编程"
subtitle: ""
date: 2022-08-15T10:01:58+08:00
draft: true
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



说函数式编程之前, 先来看看面向对象编程: 解决问题, 分解对象行为、属性, 然后通过对象的关系以及行为的调用来解决问题. 

举个🌰: 

* 对象: 用户
* 行为: 登录、连接 Jdbc、读书数据库
* 属性: 用户名、密码

**Scala 语言是一个完全面向对象语言, 万物皆对象. 对象的本质就是对数据和行为的一个封装**. 



现在再来看看函数式编程: 解决问题时, 将问题分解成各个步骤, 将每个步骤进行封装(函数), 通过调用这些封装好的步骤, 解决问题. 

举个🌰: 请求 -> 用户名、密码 -> 连接 Jdbc -> 读取数据库

**Scala 语言是一个完全函数式语言, 万物皆函数. 函数的本质就: 函数可以当做一个值进行传递**. 



在 Scala 中函数编程和面向对象**完美融合**在一起. 



## 函数基础

### 基本语法

<center>     <img style="border-radius: 0.3125em;     box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"      src="https://cdn.jsdelivr.net/gh/immustard/gallery/pictures/202208151057070.png" width = "65%" alt="" onclick="window.open(this.src)"/>     <br>     <div style="color:orange; border-bottom: 1px solid #d9d9d9;     display: inline-block;     color: #999;     padding: 2px;">       基本语法   	</div> </center>



### 函数和方法的区别

1. 核心概念

   * 为完成某一功能的程序语句的集合, 称为函数
   * 类中的函数称之为方法

2. 🌰

   1. Scala 可以在任何的语法结构中声明任何的语法
   2. 函数没有重载和重写的概念; 方法可以进行重载和重写
   3. Scala 中函数可以嵌套定义

   ```scala
   object TestFunction {
   
       // 2. 方法可以进行重载和重写, 程序可以执行
       def main(): Unit = {}
   
       def main(args: Array[String]): Unit = {
           // 1. Scala 可以在任何的语法结构中声明任何的语法
           import java.util.Date
           new Date()
   
           // 2. 函数没有重载和重写的概念, 程序报错
           def test(): Unit = {
               println("无参, 无返回值")
           }
           test()
   
           def test(name: String): Unit = {
               println()
           }
   
           // 3. Scala 中函数可以嵌套定义
           def test2(): Unit = {
               def test3(name: String): Unit = {
                   println("函数可以嵌套定义")
               }
           }
       }
   }
   ```

   

### 函数定义

```scala
def main(args: Array[String]): Unit = {
    // 函数 1: 无参, 无返回值
    def test1(): Unit = {
        println("无参, 无返回值")
    }
    test1()

    // 函数 2: 无参, 有返回值
    def test2(): String = {
        return " 无参, 有返回值"
    }
    println(test2())

    // 函数 3: 有参, 无返回值
    def test3(s: String): Unit = {
        println(s)
    }
    test3("Hello Mustard")

    // 函数 4: 有参, 有返回值
    def test4(s: String): String = {
        return s+"有参, 有返回值"
    }
    println(test4("Hello Aria"))

    // 函数 5: 多参, 无返回值
    def test5(name: String, age: Int): Unit = {
        println(s"$name, $age")
    }
    test5("Aria", 18)
}
```



### 函数参数

```scala
def main(args: Array[String]): Unit = {

    // 1. 可变参数
    def test(s: String*): Unit = {
        println(s)
    }

    // 有输入参数: 输出 Array
    test("Hello", "Mustard")
    // 无输入参数: 输出 List()
    test()

    // 2. 若参数列表中存在多个参数, 那么可变参数一般放置在最后
    def test2(name: String, s: String*): Unit = {
        println(name + "," + s)
    }

    test2("Mustard", "Aria")

    // 3. 参数默认值
    def test3(name: String, age: Int = 18): Unit = {
        println(s"$name, $age")
    }

    // 若参数传递了值, 那么会覆盖默认值
    test3("Aria", 19)
    // 若参数有默认值, 在调用时可以省略
    test3("Aria")

    // 一般, 有默认值的参数放在后面
    def test4(sex: String = "男", name: String): Unit = {
        println(s"$name, $sex")
    }
    // 4. 带名参数
    test4(name = "Mustard")
}
```



### 函数至简原则(能省则省)

```scala
def main(args: Array[String]): Unit = {

    // 0. 函数标准写法
    def f(s: String): String = {
        return "Hello " + s
    }
    println(f("Mustard"))

    // 至简原则: 能省则省

    // 1. return 可以省略, Scala 会使用函数体的最后一行代码作为返回值
    def f1(s: String): String = {
        "Hello " + s
    }

    // 2. 若函数体只有一行代码, 可以省略花括号
    def f2(s: String): String = "Hello " + s

    // 3. 返回值类型若能推断出来, 则可省略(:和返回值类型一起省略)
    def f3(s: String) = "Hello " + s

    // 4. 若函数明确声明 Unit, 那么即使函数体中使用 return 也不其作用
    def f4(): Unit = {
        return "Mustard"
    }

    // 5. Scala 如果期望是无返回值类型, 可以省略等号
    // 将无返回值的函数称为过程
    def f5() {
        "Mustard"
    }

    // 6. 若函数无参, 但声明了参数列表, 则调用时, 小括号可加可不加
    def f6() = "Mustard"
    println(f6())
    println(f6)

    // 7. 若函数没有参数列表, 则小括号可以省略, 调用时小括号必须省略
    def f7 = "Mustard"
    // println(f7())
    println(f7)

    // 8. 若不关心名称, 只关心逻辑处理, 则函数名(def)可省略
    def f8 = (x: String) => { println("Mustard") }

    def f9(f: String => Unit) = {
        f("")
    }

    f9(f8)
    println(f9((x: String) => { println("Mustard") }))
}
```



## 函数高级

### 高级函数

1. 可作为值进行传递

   ```scala
   object TestAdvancedFunction {
   
       def main(args: Array[String]): Unit = {
   
           // 1. 调用 foo 函数, 把返回值给变量 f
           // val f = foo()
           val f = foo
           println(f)
   
           // 2. 在被调用函数 foo 后面加上 _ , 相当于把函数当做一个整体, 传递给变量 f1
           val f1 = foo _
           foo()
           f1
   
           // 3. 若明确变量类型, 则不使用下划线也可将函数作为整体传递给变量
           var f2: () => Int = foo
       }
   
       def foo(): Int = {
           println("foo...")
           1
       }
   }
   ```

2. 函数可以作为参数进行传递

   ```scala
   def main(args: Array[String]): Unit = {
   
       // 1. 定义一个函数, 函数参数还是一个函数签名; f 表示函数名称; (int, Int)表示输入两个 Int 参数; Int 表示函数返回值
       def f1(f: (Int, Int) => Int): Int = {
           f(2, 3)
       }
   
       // 2. 定义一个函数, 参数和返回值类型和 f1 的输入参数一致
       def add(a: Int, b: Int): Int = a + b
   
       // 3. 将 add 函数作为参数传递给 f1 函数, 若能推断出不是调用, _ 可以省略
       println(f1(add))
       println(f1(add _))
   }
   ```

3. 函数可以作为函数返回值返回

   ```scala
   def main(args: Array[String]): Unit = {
       def f1(): (() => Unit) = {
           def f2() = {}
           f2
       }
   
       val f = f1()
       // 因为 f1 函数的返回值依然为函数, 所以可以变量 f 可以作为函数继续调用
       f()
       // 上面的代码可以简化为
       f1()()
   }
   ```

   

### 匿名函数

1. 定义

   `(x: Int) => { 函数体 }`

2. 🌰

   1. 传递的函数只有一个参数
   
      ```scala
      def main(args: Array[String]): Unit = {
      
          // 1. 定义一个函数: 参数包含数据和逻辑函数
          def operation(arr: Array[Int], op: Int => Int) = {
              for (ele <- arr) yield op(ele)
          }
      
          // 2. 定义逻辑函数
          def op(ele: Int): Int = ele + 1
      
          // 3. 标准函数调用
          val arr = operation(Array(1,2,3,4), op)
          println(arr.mkString(","))
      
          // 4. 采用匿名函数
          val arr1 = operation(Array(1,2,3,4), (ele: Int) => {
              ele + 1
          })
          println(arr1.mkString(","))
      
          // 匿名函数的至简原则
          // 4.1 参数的类型可以省略, 会根据形参进行自动推导
          val arr2 = operation(Array(1,2,3,4), (ele) => {
              ele + 1
          })
      
          // 4.2 类型省略之后, 发现只有一个参数, 则圆括号可以省略; 其他情况: 没有参数和参数超过1的永远不能省略圆括号
          val arr3 = operation(Array(1,2,3,4), ele => {
              ele + 1
          })
      
          // 4.3 匿名函数如果只有一行, 则大括号也可以省略
          val arr4 = operation(Array(1,2,3,4), ele => ele + 1)
      
          // 4.4. 若参数只出现一次, 则参数省略且后面参数可以用 _ 代替
          val arr5 = operation(Array(1,2,3,4), _ + 1)
      }
      ```
   
   2. 传递的函数有两个参数
   
      ```scala
      def main(args: Array[String]): Unit = {
      
          def calculator(a: Int, b: Int, op: (Int, Int) => Int): Int = {
              op(a, b)
          }
      
          // 1. 标准版
          calculator(2, 3, (x: Int, y: Int) => { x + y})
      
          // 2. 若只有一行, 则花括号可省略
          calculator(2, 3, (x: Int, y: Int) => x + y)
      
          // 3. 参数的类型可以省略, 会根据形参进行自动的推导
          calculator(2, 3, (x, y) => x + y)
      
          // 4. 若参数只出现一次, 则参数省略且后面参数可以用 _ 代替
          calculator(2, 3, _ + _)
      }
      ```
   
   
   
### 函数柯里化&闭包

> * **闭包**: 若一个函数访问到了它的外部(局部)变量的值, 则该函数和它所处的环境, 称为闭包. 
> * **函数柯里化**: 把一个参数列表的多个参数, 变成多个参数列表

```scala
def main(args: Array[String]): Unit = {
    def f1() = {
        var a: Int = 10
        def f2(b: Int) = {
            a + b
        }
        f2
    }

    // 在调用时, f1函数执行完毕后, 局部变量a 应该随着栈空间释放掉
    val f = f1()

    // 但是在此处, 变量a 其实并没有释放, 而是包含在了 f2函数 的内部, 形成了闭合的效果
    println(f(3))
    println(f1()(3))

    // 函数柯里化, 其实就是将复杂的参数逻辑变得简单化, 函数柯里化一定存在闭包
    def f3()(b: Int) = {
        a + b
    }
    println(f3()(3))
}
```



### 递归

**Scala 中的递归必须声明函数返回值类型**



### 控制抽象

1. 值调用: 把计算后的值传递过去
