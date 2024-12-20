---
title: "Kafka命令行操作"
subtitle: "Kafka Command Line Operation"
date: 2022-09-02T10:10:36+08:00
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
- Kafka
- message queue
- big data
categories:
- Kafka

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



## 主题命令行操作

1. 查看操作主题命令参数

   ```bash
   $ bin/kafka-topics.sh
   ```

   | **参数**                                             | **描述**                             |
   | ---------------------------------------------------- | ------------------------------------ |
   | `--bootstrap-server <String:server toconnect to>`    | 连接的 Kafka Broker 主机名称和端口号 |
   | `--topic <String:topic>`                             | 操作的 topic 名称                    |
   | `--create`                                           | 创建主题                             |
   | `--delete`                                           | 删除主题                             |
   | `--alter`                                            | 修改主题                             |
   | `--list`                                             | 查看所有主题                         |
   | `--describe`                                         | 查看主题详细描述                     |
   | `--partitions <Integer:# of partitions>`             | 设置分区数                           |
   | `--replication-factor <Integer: replication factor>` | 设置分区副本                         |
   | `--config <String:name=value>`                       | 更新系统默认配置                     |

2. 查看当前服务器中的所有 topic

   ```bash
   $ bin/kafka-topics.sh --bootstrap-server hadoop102:9092 --list
   ```

3. 创建 first topic

   ```bash
   $ bin/kafka-topics.sh --bootstrap-server hadoop102:9092 --create --partitions 1 --replication-factor 3 --topic first
   ```

4. 查看 first 主题详情

   ```bash
   $ bin/kafka-topics.sh --bootstrap-server hadoop102:9092 --describe --topic first
   ```

5. 修改分区数 **(注意: 分区数只能增加, 不能减少!!!)**

   ```bash
   $ bin/kafka-topics.sh --bootstrap-server hadoop102:9092 --alter --topic first --partitions 3
   ```

6. 删除topic

   ```bash
   $ bin/kafka-topics.sh --bootstrap-server hadoop102:9092 --delete --topic first
   ```



## 生产者命令行操作

1. 查看操作生产者命令参数

   ```bash
   $ bin/kafka-console-producer.sh
   ```

   | **参数**                                          | **描述**                             |
   | ------------------------------------------------- | ------------------------------------ |
   | `--bootstrap-server <String:server toconnect to>` | 连接的 Kafka Broker 主机名称和端口号 |
   | `--topic <String:topic>`                          | 操作的 topic 名称                    |

2. 发送消息

   ```bash
   $ bin/kafka-console-producer.sh --bootstrap-server hadoop102:9092 --topic first
   >hello world
   >hello buli-home
   ```



## 消费者命令行操作

1. 查看操作生产者命令参数

   ```bash
   $ bin/kafka-console-producer.sh
   ```

   | **参数**                                          | **描述**                             |
   | ------------------------------------------------- | ------------------------------------ |
   | `--bootstrap-server <String:server toconnect to>` | 连接的 Kafka Broker 主机名称和端口号 |
   | `--topic <String:topic>`                          | 操作的 topic 名称                    |
   | `--from-beginning`                                | 从头开始消费                         |
   | `--group <String:consumer group id>`              | 指定消费者组名称                     |

2. 消费消息

   1. 消费 first 主题中的数据

      ```bash
      $ bin/kafka-console-consumer.sh --bootstrap-server hadoop102:9092 --topic first
      ```

   2. 把主题中所有的数据都读取出来 (包括历史数据)

      ```bash
      $ bin/kafka-console-consumer.sh --bootstrap-server hadoop102:9092 --from-beginning --topic first
      ```

      

   
