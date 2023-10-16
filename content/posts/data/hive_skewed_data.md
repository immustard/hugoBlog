---
title: Hive 的数据倾斜问题
subtitle:
date: 2023-10-10T17:23:33+08:00
draft: false
author:
  name:
  link:
  email:
  avatar:
description:
keywords:
license:
comment: false
weight: 0
tags:
  - big data
  - database
  - hive
categories:
  - big data
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



> 在日常使用 Hive 的过程中, 经常会出现这样一种场景: 明明查询的时候进度条很快, 但是总是会卡在 99% 的地方. 出现这种情况往往就是因为数据倾斜导致的. 



## 什么是数据倾斜

数据倾斜在 MapReduce 中经常发生的. 简单点说就是, 在整个计算过程中, 大量相同的 key 被分配到了同一个任务上, 造成了大量的数据涌入到一个节点当中. 这也违背了分布式计算的初衷, 使得计算的整体执行效率十分低下. 



数据倾斜后的直观表现就是任务进度长时间维持在 99% (或100%), 查看监控之后会发现只有少量的 Reduce 子任务未完成. 因为其处理的数据量和其他的 Reduce 子任务差异过大, 造成最长时长远大于平均时长. 



## 什么情况下会出现数据倾斜

在日常使用中数据倾斜主要是发生在 Reduce 阶段, 很少会发生在 Map 阶段, 因为 Map 阶段的数据倾斜一般是由于 HDFS 数据存储不均匀造成的(一般存储都是均匀分块存储, 每个文件大小基本固定), 而 Reduce 阶段的数据倾斜几乎都是因为数据没有考虑到某种 key 值数据量偏多的情况而导致的. 



Reduce 阶段最容易出现数据倾斜的两个场景分别是 Join 和 Count Distinct. 



## 数据倾斜的原因

* key 分布不均匀
* 业务数据本身的特性
* 建表时考虑不周
* 某些 SQL 语句本身就有数据倾斜



## 数据倾斜的解决方案

### 优先开启负载均衡

```bash
-- map 端的 Combiner, 默认为 true
set hive.map.aggr=true;
-- 开启负载均衡 (默认为 false)
set hive.groupby.skewindata=true;
```



如果发生数据倾斜, 首先需要调整参数, 进行负载均衡处理, 这样 MapReduce 进程则会生成两个额外的 MR Job, 这两个任务的主要操作如下: 

1. MR Job 中 Map 输出的结果集首先会随机分配到 Reduce 中, 然后每个 Reduce 做局部聚合操作并输出结果, 这样处理的原因是相同的 Group By Key 有可能被分发到不同的 Reduce Job 中, 从而达到负载均衡的目的. 
2. MR Job 再根据预处理的数据结果按照 Group By Key 分布到 Reduce 中 (这个过程会保证相同的 Group By Key 被分不到同一个 Reduce 中), 最后完成聚合. 



### 表 join 连接时引发的数据倾斜

两个表进行 join 时, 如果表连接的 key 存在倾斜, 那么在 Shuffle 阶段必然会引起数据倾斜

#### 小表 join 大表, 某个 key 过大

通常做法是将倾斜的数据存到分布式缓存中, 分发到各个 Map 任务所在节点. 在 Map 阶段完成 join 操作, 即 MapJoin, 这样就避免了 Shuffle, 从而避免了数据倾斜. 



MapJoin 是 Hive 的一种优化操作, 其适用于小表 Join 大表的场景, 由于表的 Join 操作是在 Map 端且在内存尽情的, 所以其并不需要启动 Reduce 任务也就不需要经过 Shuffle 阶段, 从而能在一定程度上节省资源提高 Join 效率. 



在 Hive v0.11 之前, 如果想在 Map 阶段完成 join 操作, 必须使用 MAPJOIN 来标记显式的启动该优化操作, 由于其需要将小表加载进内存所以要注意小表的大小. 



```sql
-- 常规 join
SELECT 
	pis.id_ id,
	service_name serviceName,
	service_code serviceCode,
	pip.code_text serviceType
FROM
	prd_price_increment_service pis
	LEFT JOIN prd_price_increment_product pip on pip.increment_service_id = pis.id_;
	
-- Hive v0.11 之前开启 MapJoin
-- 将小表 prd_price_increment_service 放到 map 端的内存中
-- 如果想将多个表放在 Map 端内存中, 只需在 mapjoin() 中写多个表名称即可, 用逗号分割
SELECT
	/*+ mapjoin(pis) */
	pis.id_ id,
	service_name serviceName,
	service_code serviceCode,
	pip.code_text serviceType
FROM
	prd_price_increment_service pis
	LEFT JOIN prd_price_increment_product pip on pip.increment_service_id = pis.id_;
```



在 v0.11 及之后, Hive 默认启动该优化, 不需要显式的使用 MAPJOIN 标记, 可以通过下面两个属性来设置该优化的出发时机:

```sql
-- 自动开启 MAPJOIN 优化, 默认为 true
set hive.auto.convert.join=true;
-- 确定使用该优化的表的大小, 如果表的大小小于此值就会被加载到内存中, 默认为 25000000 (25M)
set hive.mapjoin.smalltable.filesize=25000000;

SELECT 
	pis.id_ id,
	service_name serviceName,
	service_code serviceCode,
	pip.code_text serviceType
FROM
	prd_price_increment_service pis
	LEFT JOIN prd_price_increment_product pip on pip.increment_service_id = pis.id_;
	
-- 特殊说明
-- 使用默认启动该优化的方式如果出现莫名其妙的 bug (比如 MAPJOIN 不起作用), 就将以下两个属性置为 false
-- 
-- 关闭自动 MAPJOIN 转换操作
set hive.auto.convert.join=false;
-- 不忽略 MAPJOIN 标记
set hive.ignore.mapjoin.hint=false;

SELECT
	/*+ mapjoin(pis) */
	pis.id_ id,
	service_name serviceName,
	service_code serviceCode,
	pip.code_text serviceType
FROM
	prd_price_increment_service pis
	LEFT JOIN prd_price_increment_product pip on pip.increment_service_id = pis.id_;
```



> 将表放在 Map 端的内存时, 如果节点的内存很大, 但还是出现内存溢出的情况, 可以通过参数 `mapreduce.map.memory.mb` 调节 Map 端内存的大小. 



#### 表中作为关联条件的字段值为 0 或空值的较多

```sql
-- 方案一: 给空值添加随机 key 值, 将其发放到不同的 reduce 中处理. 由于 null 值关联不上, 所以对结果无影响. 
SELECT 
	*
FROM log a
LEFT JOIN users b
ON (CASE WHEN a.user_id IS NULL THEN CONCAT('hive', rand()) ELSE a.user_id END) = b.user_id;

-- 方案二: 去重空值
SELECT 
	a.*,
	b.name
FROM (
	SELECT * FROM users WHERE LENGTH(user_id) > 1 OR user_id IS NOT NULL
) a
JOIN (
	SELECT * FROM log WHERE LENGTH(user_id) > 1 OR user_id IS NOT NULL
) b
ON a.user_id = b.user_id;
```


#### 表中作为关联条件的字段重复值过多

```sql
SELECT 
	a.*,
	b.name
FROM (
	SELECT * FROM users WHERE LENGTH(user_id) > 1 OR user_id IS NOT NULL) a
) a
JOIN (
	SELECT * FROM (SELECT *, row_number() over(partition by user_id order by create_time desc) rk FROM log WHERE LENGTH(user_id) > 1 OR user_id IS NOT NULL) t where rk = 1
) b
```


### 空值引发的数据倾斜

实际业务中有些大量的 `null` 值或者一些无意义的数据参与到计算作业中, 表中有大量的 `null` 值, 如果表之间进行 JOIN 操作, 就会有 Shuffle 产生, 这样所有的 `null` 值都会被分配到一个 reduce 中, 所以就一定会产生数据倾斜. 

这里有一个问题: 如果 A, B 两表进行 JOIN 操作, 如果 A 表中需要 JOIN 的字段为 `null`, 但是 B 表中需要 JOIN 的字段不为 `null`, 这两个字段根本就 JOIN 部上, 但是还是会放到一个 reduce 中. 这是因为, 数据放到同一个 reduce 中的原因不是因为字段能不能 JOIN 上, 而是因为 Shuffle 阶段的 hash 操作, 只有 key 的 hash 是一样的, 就会被放到同一个 reduce 中. 

```sql
-- 解决方案
-- 场景: 🌰 日志中, 经常会有信息丢失的问题, 比如日志中的 user_id, 如果取其中的 user_id 和用户表中的 user_id 关联, 会碰到数据倾斜的问题
-- 方案一: 可以直接不让 null 值参与 JOIN 操作, 即不让 null 值有 Shuffle 阶段, 所以 user_id 为空的不参与关联
SELECT 
	*
FROM log a 
JOIN users b
ON a.user_id IS NOT NULL AND a.user_id = b.user_id
UNION ALL
SELECT 
	*
FROM log a 
WHERE a.user_id IS NULL;

-- 方案二: 因为 null 值参与 Shuffle 时的 hash 结果是一样的, 那么我们可以给 null 值随机赋值, 这样它们的 hash 结果就不一致, 就会进到不同的 reduce 中
SELECT
	*
FROM log a
LEFT JOIN users b
ON (CASE WHEN a.user_id IS NULL THEN CONCAT('hvie_', rand()) ELSE a.user_id END) = b.user_id
```

> 针对上述方案进行分析, 方案二比方案一的效率更高, 不但 IO 少了, 而且作业数也少了. 方案一中对 log 读取两次,  jobs 为 2. 方案二 job 数是 1. 这个优化适合对无效 id (-99, '', null 等) 产生的倾斜问题. 把空值的 key 变成一个字符串加上随机数, 就能把倾斜的数据分到不同的 reduce 上, 从而解决倾斜的问题. 


### 不同数据类型关联产生数据倾斜

对于两个表 JOIN, 表 a 中需要 JOIN 的字段 key 为 int, 表 b 中 key 字段既有 string 类型也有 int 类型. 当按照 key 进行两个表的 JOIN 操作时, 默认的 hash 操作会按 int 类型的 id 来进行分配, 这样所有的 string 类型都将分配成同一个 id, 结果就是所有的 string 类型的字段进入到一个 reduce 中, 从而造成数据倾斜. 

```sql
-- 如果 key 字段既有 string 类型也有 int 类型, 那么就直接都转成 string 类型, hash 时就会按照 string 类型分配了
-- 方案一: 把数字类型转成字符串类型
SELECT * FROM users a LEFT JOIN logs b ON a.user_id = CAST(b.user_id AS string);

-- 方案二: 建表时按照规范建设, 统一词根, 同一词根数据类型一致
```


### COUNT DISTINCT 大量相同特殊值

由于 SQL 中的 DISTINCT 操作本身会有一个全局排序的过程, 一般情况下, 不建议采用 COUNT DISTINCT 方式进行去重计数, 除非表的数量较小. 

* 当 SQL 中不存在分组字段时, COUNT DISTINCT 操作仅生成一个 reduce 任务, 该任务对全部数据进行去重统计; 
* 当 SQL 中存在分组字段时, 可能某些 reduce 任务需要去重统计的数量非常大;

```sql
-- 可能造成倾斜的 SQL
SELECT a, COUNT(DISTINCT b) FROM t GROUP BY a;

-- 先去重, 然后分组统计
SELECT a, sum(1) FROM (SELECT a, b FROM t GROUP BY a, b) GROUP BY a;
```

> 总结: 如果分组统计的数据存在多个 DISTINCT 结果, 可以先将数值为空的数据占位处理, 分 SQL 统计数据, 然后将两组结果 UNION ALL 进行汇总结算. 


### 数据膨胀引发的数据倾斜

在多维聚合计算时, 如果进行分组聚合的字段过多, 且数据量很大, map 端的聚合不能很好地起到数据压缩的情况下, 会导致 map 产出的数据急速膨胀, 这种情况容易导致作业内存溢出的异常. 如果 log 表含有数据倾斜 key, 会家具 Shuffle 过程的倾斜. 

```sql
-- 造成倾斜或内存溢出的情况
-- SQL 01
SELECT a, b, c, COUNT(1) FROM log GROUP BY  a, b, c WITH ROLLUP;
-- SQL 02
SELECT a, b, c, COUNT(1) FROM log GROUPING SETS a, b, c;

-- 解决方案
-- 可以拆分上面的 SQL, 将 WITH ROLLUP 拆分成几个 SQL
SELECT 
	a, b, c sum(1)
FROM (
	SELECT a, b, c COUNT(1) FROM log GROUP BY a, b, c
	UNION ALL
	SELECT a, b, NULL, COUNT(1) FROM log GROUP BY a, b
	UNION ALL
	SELECT a, NULL, NULL, COUNT(1) FROM log GROUP BY a
	UNION ALL
	SELECT NULL, NULL, NULL COUNT(1) FROM log 
) t;
-- 但是这种方式不太友好, 因为这是对 3 个字段进行分组聚合, 但如果是更多的字段就很麻烦了. 

-- 在 Hive 中可以通过参数 hive.new.job.grouping.set.cardinality 配置的方式自动控制作业的拆解, 该参数默认值是 30
-- 该参数主要针对 GROUPING SETS/ROLLUPS/CUBES 这类多维聚合的操作生效, 如果最后拆解的键组合大于该值, 会启用新的任务去处理大于该值之外的组合. 如果在处理数据时, 某个分组聚合的列有较大的倾斜, 可以适当调小该值. 
set hive.new.job.grouping.set.cardinality=10;
SELECT a, b, c, count(1) FROM log GROUP BY a, b, c, WITH ROLLUP;
```


## 总结

说到底, 解决 reduce 端数据倾斜的必然途径就是让 map 端的输出数据更均匀地分布到 reduce 中. 

在此过程中, 掌握四点可以更好的解决数据倾斜问题: 

1. 如果任务长时间卡在 99% 则基本可以认为是发生了数据倾斜, 建议调整参数以实现负载均衡: `set hive.groupby.skewindata=true`
2. 小表关联大表操作, 需要先看能否使用子查询, 再看能否使用 MAPJOIN
3. JOIN 操作注意关联字段不能出现大量的重复值或者空值
4. COUNT(DISTINCT id) 去重统计用慎用, 尽量通过其它方式替换
