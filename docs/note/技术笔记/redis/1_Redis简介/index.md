# Redis的简介(REmote  DIctionary Server)

## 简介

Redis是一种开源（BSD 许可）内存数据结构存储，用作数据库、缓存、消息代理和流引擎。

Redis 提供数据结构，*例如 字符串(strings)、散列(hashes)、列表(lists)、集合(sets)、带范围查询的排序集合(sorted sets)、 bitmaps、hyperloglogs、地理空间索引和流(geospatial indexes)。*

Redis 具有内置复制、Lua 脚本、LRU 逐出、事务和不同级别的磁盘持久性，并通过Redis Sentinel和Redis Cluster的自动分区提供高可用性。

对这些类型的操作都是原子性的操作。
*如：append字符串,递增hash中的值，push元素到列表，计算集合交集、并集和茶几，或者获取排序集合中排名最高的成员。*

为了获得最佳性能，Redis 使用 **内存中的数据集**。根据用例，Redis 可以通过定期将数据集转储到磁盘 或通过将每个命令附加到基于磁盘的日志来持久保存数据。如果只需要功能丰富的联网内存缓存，也可以禁用持久性。

Redis 是用ANSI C编写的，适用于大多数 POSIX 系统，如 Linux、*BSD 和 Mac OS X，无需外部依赖。Linux和OS X是Redis开发和测试最多的两个操作系统，**推荐使用Linux进行部署**

## C#客户端开源库

### StackExchange.Redis

#### 概述

 StackExchange.Redis 是用于 .NET 语言（C# 等）的高性能通用 Redis 客户端。它是BookSleeve的逻辑继承者，并且是由Stack Exchange为Stack Overflow等繁忙站点开发（和使用）的客户端。

#### 特征

1. 高性能多路复用设计，允许高效使用来自多个调用线程的共享连接
2. Redis 节点配置的抽象：客户端可以静默协商多个 Redis 服务器的健壮性和可用性
3. 方便地访问完整的 redis 功能集
4. 同步和异步使用的完整双编程模型，不需要TPL的“同步优于异步”使用
5. 支持redis“集群” 

[github地址](https://github.com/StackExchange/StackExchange.Redis)
[文档地址](https://stackexchange.github.io/StackExchange.Redis/)
