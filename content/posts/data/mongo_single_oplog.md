---
title: MongoDB 单节点开启 oplog
subtitle:
date: 2023-10-16T12:48:45+08:00
draft: false
author:
  name: Mustard	
  link: https://www.buli-home.cn
  email: mustard_gxg@foxmail.com
  avatar: https://pub-7360a7072ee341a58e1e9b6541edca66.r2.dev/portrait/mustard.png
description:
keywords:
license:
comment: false
weight: 0
tags:
  - big data
  - database
  - mongodb
categories:
  - database
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



> MongoDB 单节点是没有 oplog 的, 但是在测试的时候或者在生产环境, 是有存在单节点的情况的. 这个文档介绍一下是怎么单节点开启 oplog 的.

## 修改配置文件

在 `mongodb.conf` 中添加一行

```conf
replSet=single
```

## 重启 MongoDB

### 停止 MongoDB

```shell
> use admin
switched to db admin
> db.shutdownServer()
```

如果报错这个错误的话, 需要添加权限: 

```shell
2023-10-16T11:15:16.895+0800 E QUERY    [js] Error: shutdownServer failed: {
        "ok" : 0,
        "errmsg" : "not authorized on admin to execute command { shutdown: 1.0, lsid: { id: UUID(\"e5e69fbd-9e73-4e49-b43d-ffd71b82075a\") }, $db: \"admin\" }",
        "code" : 13,
        "codeName" : "Unauthorized"
} :
_getErrorWithCode@src/mongo/shell/utils.js:25:13
DB.prototype.shutdownServer@src/mongo/shell/db.js:454:1
@(shell):1:1

> db.grantRolesToUser( "mustard" , [ { role: "hostManager", db: "admin" } ])
```

### 启动 MongoDB

```shell
# ./mongod --config mongodb.conf --fork
```

## 初始化副本集的脚本

需要在 `local` 或者 `admin` 数据库中执行: 

```shell
> rs.initiate({ _id: "副本集名称", members: [{_id:0,host:"服务器的IP:Mongo的端口号"}]})
```

> 如果报这个错误则, 需要添加权限: 
>
> ```shell
> {
> 		"ok" : 0,
> 		"errmsg" : "not authorized on admin to execute command { replSetInitiate: { _id: \"single\", members: [ { _id: 0.0, host: \"10.140.6.26:27017\" } ] }, lsid: { id: UUID(\"7fdda9e0-3a2b-469b-93b6-e5e92f2f4997\") }, $db: \"admin\" }",
> 		"code" : 13,
> 		"codeName" : "Unauthorized"
> }
> 
> > db.grantRolesToUser( "mustard" , [ { role: "clusterManager", db: "admin" } ])
> ```

初始完, 副本集中唯一的节点, 可能短时间显示为 `SECONDARY` 或 `OTHER`. 一般而言, 稍等一会, 就会自然恢复为 `PRIMARY`, 无需人工干预. 
