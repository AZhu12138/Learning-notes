# NoSQL数据库分类

**kv键值对：**

- redis
- tair
- mecache

**文档数据库：**

- MongoDB
  - 分布式，C++编写，处理大量的文档
  - 介于关系型和非关系型之间
- ConthDB

**列存储数据库：**

- HBase
- 分布式文件系统

**图关系数据库：**

- 不是存图形，是存关系的：社交网络、广告推荐
- Neo4j，InfoGrid

# Redis入门

## 概述

### redis是啥？

Redis（Remote Dictionary Server )，即**远程字典服务**，

- 是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，
- 并提供多种语言的API。
- 免费、开源、结构化数据库

### redis可以干嘛？

- 内存存储、持久化
- 效率高，高速缓存
- 发布订阅系统
- 地图信息分析
- 计时器、计数器、浏览量
- 、、、

### 特性：

1. 多样的数据类型
2. 持久化
3. 集群
4. 事务

## Windows下安装

1. 下载安装包：https://github.com/dmajkic/redis/downloads

2. 解压：

   ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210616201916.png)

3. 开启服务

4. 开启客户端，连接服务端

5. 进行操作：

   ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210616202058.png)



## Linux下安装

1. 下载安装包：https://redis.io/download
2. 解压
3.  