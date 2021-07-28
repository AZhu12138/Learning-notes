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

下载安装包：https://redis.io/download

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210724221227.png)

在解压前，

**需要安装`c`的编译环境：**

测试以下是否有`c`的编译环境：

```
# gcc --version
```

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210724221524.png)

如上图就是已经有了，

如果没有，就通过以下命令安装：

```
# yum install gcc
```

安装好之后，就需要：

**解压：**

```
# tar -zxvf redis-6.2.5.tar.gz
```

**编译：**

需要进入到`redis-6.2.5`文件夹：

```
# cd redis-6.2.5/

# make
```

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210724222146.png)

编译成功。

**安装：**

依旧在`redis-6.2.5`目录下：

```
# make install
```

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210724222348.png)

最后会默认安装在：/usr/local/bin 目录下

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210724222535.png)



### 安装目录介绍：

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210724222751.png)

- redis-benchmark：性能测试工具，可以在自己机器上运行
- redis-check-aof：修复有问题的aof文件
- redis-check-dump：修复有问题的dump.rdb文件
- redis-sentinel：redis集群使用
- **redis-server：redis服务端入口**
- **redis-cli：redis客户端入口**

### 服务端启动：

有两种方式启动：

- 前台启动：
- 后台启动：

#### 前台启动：

在`/usr/local/bin`目录下，直接运行服务端文件：

```
# redis-server
```

会一直保持在服务端的页面，不能在该页面进行其他命令行操作，所以推荐使用后台启动方式。

#### 后台启动：

首先，修改redis的配置文件，改为以后台方式启动：

1. 将`/opt/redis-6.2.5/`的`redis.conf`文件复制到`/etc`下，

   ```
   # cp /opt/redis-6.2.5/redis.conf /etc/redis.conf
   ```

   不复制也可以，但是遵循Linux文件结构规范：配置文件应该放到`/etc/`下。

   ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210725152001.png)

2. 修改`/etc/redis.conf`文件：将`daemonize no`改为`daemonize yes`

   ```
   # vim /etc/redis.conf
   ```

   ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210725152148.png)

修改后之后就在`/usr/local/bin`目录下启动服务端：

```
# redis-server /etc/redis.conf
```

然后就启动成功。

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210725152412.png)

### 客户端启动：

在`/usr/local/bin`目录下，直接运行客户端文件：

```
# redis-cli
```

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210725152527.png)

### 后台启动的关闭：

查看redis的后台进程：

```
# ps -ef | grep redis
```

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210725152617.png)

然后直接通过进程号杀掉：

```
# kill -9 10226
```

或者：

在客户端连接上服务端之后，执行以下命令，也可以关掉redis服务：

```
# shutdown
```

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210725152759.png)





