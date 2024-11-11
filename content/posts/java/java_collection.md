---
title: "Java集合"
subtitle: ""
date: 2022-02-24T20:15:26+08:00
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
- Java
categories:
- Java

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



> 今天进行了一场面试, 面试官在问我关于`HashMap`的时候, 感觉自己回答的不是很好, 所以现在索性就梳理一下Java关于集合的这部分知识.
>
> 主要是问了这么几个问题:
>
> 1. `HashMap`是线程安全的么
> 2. 那线程安全的map是哪种?
> 3. 在定义`HashMap`的时候会有定义长度的习惯么? 
> 4. `HashMap`的底层是怎么实现的? 
> 5. `HashMap`是如何存储的? 
> 6. `HashMap`最大长度是多少? 或者说是达到多大的长度就需要扩容了?  (这个没答上来...)



{{< html >}}<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="https://webp.buli-home.cn/2022/02/202202242122780.png" width = "85%" alt="" onclick="window.open(this.src)"/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      说到Java的Collection就一定会放出这张神图
  	</div>
</center>{{< /html >}}





根据这张图能发现, 这一切的一切都起始于`Iterable`接口. 



## Iterable

从源码里能看到, 这个接口允许对象成为`for-each`的循环目标, 也就是增强型`for`循环, 是Java中的一种[语法糖](../syntacticsugar). 

```java
List<Object> list = new ArrayList();
// 补充: 数组也可`for-each`遍历
// Object[] list = new Object[5];

for (Object obj: list) {}
```

#### 其他遍历方式

**JDK 1.8** 之前, `Iterable`只有一个方法:

```java
Iterator<T> iterator();
```

这个接口能够创建一个轻量级的迭代器, 用于**安全的**遍历元素, 移除元素, 添加元素. 其中涉及了一个概念就是[fail-fast](../failfast).

总结起来就是: **能创建迭代器进行元素添加和删除的话, 就尽量使用迭代器进行添加和删除操作**.

```java
for (Iterator it = list.iterator(); it.hasNext(); ) {
	System.out.println(it.next());
}
```



## 顶层接口

`Collection`是一个顶层接口, 主要是用来定义集合的约定. 

`List`接口也是一个顶层接口, 继承了`Collection`接口, 同时也是`ArrayList`, `LinkedList`等集合元素的父类. 

`Set`接口位于与`List`接口同级的层次上, 它同时也继承了`Collection`接口. `Set`接口提供了额外的规定. 对`add()`, `equals()`, `hashCode()`方法提供了额外的标准. 

`Queue`是和`List`, `Set`接口并列的`Collection`的**三大接口**之一. `Queue`的设计用来在处理之前保持元素的访问次序. 除了`Collection`基础的操作外, 对立提供了额外的插入, 读取, 检查操作. 

`SortSet`接口直接继承与`Set`接口,  使用`Comparable`对元素进行自然排序或者使用`Comparator`在创建时对元素提供**定制的**排序规则. `set`的迭代器将按升序元素顺序遍历集合. 

`Map`是一个支持`key-value`存储的对象, `Map`不能包含重复的`key`, 每个键最多映射一个值. 这个接口替代了`Dictionary`类, `Dictionary`是一个抽象类而不是接口. 



## ArrayList

`ArrayList`是实现`List`接口的**可扩容数组(动态数组)**, 它的内部是基于数组实现的, 具体的定义: 

```java
public class ArrayList<E> extends AbstractList<E> implements List<E>, RandomAccess, Cloneable, java.io.Serializable {...}
```

* `ArrayList`可以实现所有可选择的列表操作, 允许所有元素 **(包括** `null`**)**. `ArrayList`还提供了内部存储`list`的方法, 它能够完全替代`Vector`, 只有一点例外, `ArrayList`**不是线程安全的容器**. 

  > 下面会说到`Vector`

* `ArrayList`有一个容量的概念, 这个数组的容量就是`List`用来存储元素的容量. 

  > 在不声明容量的时候, 默认的是10. 当达到当前容量上限的时候, 就会进行扩容, 负载因子为0.5, 即:
  >
  > ```
  > 旧容量 * 1.5 ==> 10->15->22->33...
  > ```

  `ArrayList`的上限为`Integer.MAX_VALUE - 8`(2<sup>32</sup> - 8). 

* `ArrayList`不是线程安全的容器, 所以可以使用线程安全的`List`:

  ```java
  List list = Collections.synchronizedList(new ArrayList<>());
  ```

* `ArrayList`具有[fail-fast](../failfast)快速失败机制, 当在迭代集合的过程中, 该集合发成了改变的时候, 就**可能**会发生`fail-fast`, 抛出`ConcurrentModificationException`异常. 



## Vector

Java中的[Vector](https://baike.baidu.com/item/Vector/3330482#1_2)类是允许不同类型共存的变长数组, Java.util.Vector提供了向量(`Vector`)类以实现类似动态数组的功能. 在相对于`ArrayList`来说, `Vector`线程是安全的, 也就是说是同步的. 因为`Vector`对内部的每个方法都是简单粗暴的上锁, 所以访问元素的效率**远远低于**`ArrayList`. 

还有一点在扩容上, `ArrayList`扩容后的数组长度会增加50%, 而`Vector`的扩容长度后数组是翻倍. 



## LinkedList

`LinkedList`是一个双向链表, 允许所有元素 **(包括** `null`**)**:

* `LinkedList`所有的操作都可以表现为**双向性**, 索引到链表的操作将遍历从头到尾, 看那个距离短为遍历顺序. 

* `LinkedList`不是线程安全的容器, 所以可以使用线程安全的`Set`:

  ```java
  List list = Collections.synchronizedList(new LinkedList<>());
  ```

* 因为`LinkedList`是一个双向链表, 所以没有初始化大小, 没有扩容机制. 



## Stack

堆栈(`Stack`)就是常说的**后入先出**的容器. 它继承了`Vector`类, 提供了常用的`pop`, `push`和`peek`操作, 以及判断`stack`是否为空的`empty`方法和寻找与栈顶距离的`search`方法. 



## HashSet

* `HashSet`是`Set`接口的实现类, 有哈希表支持 **(实际上**`HashSet`**是**`HashMap`**的一个实例)**, 不能保证集合的迭代顺序. 允许所有元素 **(包括** `null`**)**. 

* `HashSet`不是线程安全的容器, 所以可以使用线程安全的`Set`:

  ```java
  Set set = Collections.synchronizedSet(new HashSet<>());
  ```

* 支持[fail-fast](../failfast)机制. 

* 因为`HashSet`的底层实际使用`HashMap`实现的, 所以和`HashMap`的容量和扩容机制是一致的: 

  > 在不声明容量的时候, 默认的是16. 当达到当前容量上限的时候, 就会进行扩容, 负载因子为0.75, 即:
  >
  > ```
  > 旧容量 * 1.75 ==> 16->28->49->85...
  > ```



## TreeSet

`TreeSet`是一个基于`TreeMap`的`NavigableSet`实现. 这些元素使用他们的自然排序或者在创建时提供的`Comparator`进行排序, 具体取决于使用的构造函数. 

* 此实现为基本操作`add`, `remove`, `contains`提供了`log(n)`的时间成本. 
* `HashSet`不是线程安全的容器. 

* 支持[fail-fast](../failfast)机制. 



## LinkedHashSet

{{< html >}}<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="https://webp.buli-home.cn/2022/02/202202260717202.png" width = "65%" alt="" onclick="window.open(this.src)"/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      LinkedHashSet继承体系
  	</div>
</center>{{< /html >}}



`LinkedHashSet`是`Set`接口的`Hash`表和`LinkedList`的实现. 但是这个实现不同于`HashSet`的是, 它维护者一个贯穿所有条目的双向列表. 此链表定义了元素插入集合的顺序. **注意: 如果元素重新插入, 则插入顺序不会受到影响**. 

* `LinkedHashSet`有两个影响其构成的参数: 初始容量和负载因子. 它们的定义与`HashSet`完全相同. 但是对于`LinkedHashSet`, 选择过高的初始容量值的开销要比`HashSet`小, 因为`LinkedHashSet`的迭代次数不收容量影响. 
* `LinkedHashSet`不是线程安全的容器. 

* 支持[fail-fast](../failfast)机制. 



## PriorityQueue

`PriorityQueue`(优先级队列)是`AbstractQueue`的实现类, 其中的元素根据自然排序(最小元素最先出)或者通过构造函数时期提供的`Comparator`来排序, 具体根据构造器判断. **注意**: `PriorityQueue`**不允许**`null`**元素**. 

* 队列的头在某种意义上是指定顺序的最后一个元素. 队列查找操作`poll`, `remove`, `peek`和`element`访问队列头部元素. 
* `PriorityQueue`是无界队列(无限制的), 但是有内部`capacity`, 用户控制用于在队列中存储元素的数组大小. 
* 该类以及迭代器实现了`Collection`, `Iterator`接口的所有可选方法. 这个迭代器提供了`iterator()`方法不能保证以任何特定顺序遍历`PriorityQueue`. 如果需要有序遍历的话, 可以考虑使用`Arrays.sort(pq.toArray())`. 
* `PriorityQueue`必须存储的可比较的对象, 如果不是的话, 则必须指定比较器. 
* `PriorityQueue`不是线程安全的容器, 而线程安全的类是`PriorityBlockingQueue`. 



## HashMap

`HashMap`是一个利用哈希表原理来存储元素的集合, 允许空的`key-value`键值对. 

* `HashMap`的默认初始用量和负载因子和`HashSet`一致. 
* `HashMap`不是线程安全的容器. 



## TreeMap

一个基于`NavigableMap`实现的红黑树. 这个`map`根据`key`自认排序存储, 或者通过`Comparator`进行定制排序. 

* `TreeMap`为`containsKey`,`get`,`put`和`remove`方法提供了`log(n)`的时间开销. 

* `TreeMap`不是线程安全的容器. 

* 支持[fail-fast](../failfast)机制. 



## LinkedHashMap

`LinkedHashMap`是`Map`接口的哈希表和链表的实现. 这个实现与`HashMap`的不同之处在于它维护了一个贯穿其所有条目的双向链表. 这个链表中定义的顺序, 通常是插入的顺序. 

* 提供了一个特殊的构造器: `LinkedHashMap(int,float,boolean)`, 其遍历的顺序是其最后一次访问的顺序. 
* 可以重写`removeEldestEntry(Map.Entry)`方法, 以便在将新映射添加到`map`时强制删除过期映射的策略. 
* 这个类提供了所有可选择的`map`操作, 并且允许`null`元素. 由于维护链表的额外开销, 性能**可能**会低于`HashMap`, 有一条除外: 遍历`LinkedHashMap`中的`collection-views`需要与`map.size`成正比, 无论其容量如何. `HashMap`的迭代看起来开销更大, 因为还要求时间与其容量成正比.
*  `LinkedHashMap`有两个因素影响了它的构成: 初始容量和负载因子. 

* `LinkedHashMap`不是线程安全的容器. 

* 支持[fail-fast](../failfast)机制. 



## HashTable

与`HashMap`不同的是, `HashTable`**是线程安全的**. 任何非空对象都可以用作键或值. 

* 支持[fail-fast](../failfast)机制. 



## IdentityHashMap

`IdentityHashMap` 是一个比较小众的`Map`实现类. 

* `IdentityHashMap`不是一个通用的`Map`实现, 虽然这个类实现了`Map`接口, 但是它故意违反了`Map`的约定, 该约定要求在比较对象时使用`equals`方法, 此类仅适用于需要引用相等语义的极少数情况. 

* `IdentityHashMap`不是线程安全的容器. 

* 支持[fail-fast](../failfast)机制. 



## WeakHashMap

`WeakHashMap`类基于哈希表的`Map`基础实现, 带有弱键. `WeakHashMap`中的`entry`当不再使用时还会自动移除. 也就是说, `WeakHashMap`中的`entry`不会增加其引用计数.

* 基于`map`接口, 是一种弱键相连, `WeakHashMap`里面的键会自动回收. 
* 支持`null`键和`null`值.
* 支持`fail-fast`机制
* `WeakHashMap`经常用作缓存. 



## 集合实现类特征图

下面这个表格汇总了部分集合框架的主要实现类的特征



|         集合         | 排序 | 随机访问 | `key-value`存储 | 重复元素 | 空元素 | 线程安全 |
| :------------------: | :--: | :------: | :-------------: | :------: | :----: | :------: |
|      ArrayList       |     |         |                |         |       |         |
|      LinkedList      |     |         |                |         |       |         |
|       HashSet        |     |         |                |         |       |         |
|       TreeSet        |     |         |                |         |       |         |
|       HashMap        |     |         |                |         |       |         |
|       TreeMap        |     |         |                |         |       |         |
|        Vector        |     |         |                |         |       |         |
|      HashTable       |     |         |                |         |       |         |
|  ConcurrentHashMap   |     |         |                |         |       |         |
|        Stack         |     |         |                |         |       |         |
| CopyOnWriteArrayList |     |         |                |         |       |         |

