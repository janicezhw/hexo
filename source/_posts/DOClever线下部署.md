---
title: DOClever线下部署
date: 2019-05-06 15:00:23
tags:
---

> 线下部署源码下载及安装步骤  https://github.com/sx1989827/DOClever

<!-- more --> 

## 环境准备

### Node.js 安装：

https://nodejs.org/en/  MacOS下载 pkg包安装（注意安装推荐版本，当前DOClever需要8.11.1 node环境，否则无法正常安装https://nodejs.org/dist/v8.11.1/  ）

### Mongodb

https://www.mongodb.com/
推荐通过homebrew安装
(需要先安装homebrewhttps://brew.sh/)

```
brew install mongodb
```

创建数据库目录

```
mkdir -p /data/db
```

启动Mongodb

```
mongod
```

安装Mongodb客户端robomongo https://robomongo.org/

打开Robomongo , create 数据库 DOClever

## DOClever安装

### ~~npm部署 （后续遇到问题，不推荐）~~

```
npm install doclever -g
```

### 源码部署

下载DOClever源码https://github.com/sx1989827/DOClever

```
node Downloads/DOClever-master/Server/bin/www
```

```
mongodb://localhost:27010/DOClever
```

上传路径： /User/Shared/DOClever

端口：12000

安装完成后即可通过 http://localhost:12000 访问