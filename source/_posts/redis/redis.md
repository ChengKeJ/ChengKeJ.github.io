---
layout: post
title:  "Redis — 不仅仅是缓存"
categories: redis
tags: redis
keywords: redis,缓存,Redis事务,Redis支持的数据结构,Redis持久化
date: 2023/10/14 20:46:25
---


![1*qIy3PMmEWNcD9Czh_21C8g.png](https://miro.medium.com/v2/resize:fit:1400/1*qIy3PMmEWNcD9Czh_21C8g.png)

Redis是一种快速、开源的内存键值（NoSQL）数据库，远远超越了缓存的功能。Redis使用RAM进行操作，提供亚毫秒级的响应时间，支持每秒数百万次请求。Redis主要用于缓存，但它也可以作为那些数据不经常变化的应用程序的主要数据库。Redis内置了复制、服务端脚本（使用Lua脚本）、LRU驱逐、定时过期和事务支持。

Redis的用例包括：

- 数据投影服务
- 临时消息代理
- 事件溯源系统

*注意：Redis是使用C语言编写的。*

<!--more-->

## Redis为什么快？

- Redis是基于RAM的。RAM访问速度比随机磁盘访问快。
- 使用IO多路复用和单线程执行循环来提高执行效率。
- 使用高效的底层数据结构（SDS、ZipList、SkipList、分层索引）。

## Linux安装步骤

创建文件`/etc/yum.repos.d/redis.repo`，内容如下：

```other
[Redis]
name=Redis
baseurl=http://packages.redis.io/rpm/rhel7
enabled=1
gpgcheck=1
```

然后运行以下命令：

```other
curl -fsSL https://packages.redis.io/gpg > /tmp/redis.key
sudo rpm --import /tmp/redis.key
sudo yum install epel-release
sudo yum install redis-stack-server
```

## 高级CLI命令

```other
$ redis-cli

INFO - 获取有关集群的信息
SELECT - 选择命名空间/数据库
DBSIZE - 获取键的数量
KEYS * - 列出所有键（模式匹配）（优先使用SCAN命令）
SCAN - 用游标返回结果的子集
EXISTS - 检查键是否存在
TYPE - 检查键的数据类型
EXPIRE - 设置键的过期时间
RENAME - 将键重命名为新名称
FLUSHDB - 清除命名空间中的所有键
FLUSHALL - 清除所有命名空间中的所有键
ROLE - 检查节点角色（主节点、从节点、哨兵节点）
CLEAR - 清除CLI上下文终端
QUIT - 退出CLI

# redis-cli --pipe #从文件或stdin流式传输命令到Redis
# redis-cli --hotkeys #查找热键
```

## 关于一些命令的几点说明

- Redis数据库是创建键的逻辑方式，用于创建键的隔离和命名空间。默认情况下，Redis有0-15个数据库索引。在集群模式下，这些数据库不存在。

```other
SELECT index_no
```

- 以下命令通常会添加

后缀。这些命令根据键的存在与否采取不同的行为。

```other
NX - 如果不存在
XX - 如果存在
```

注意：通常在命令前加上"M"表示多，例如MGET与GET。类似地，在命令前加上"P"表示基于模式的命令。

- KEYS命令是顺序的（O(n)）和同步的（阻塞的）。在应用程序中，优先使用SCAN而不是KEYS。

```other
RedisInsight - 这是一个与Redis服务器交互的用户界面工具。
```

## 支持的数据结构

![1*f96hoFTEAiCt2jCF-s7EmA.png](https://miro.medium.com/v2/resize:fit:1400/1*f96hoFTEAiCt2jCF-s7EmA.png)

## String（字符串）

- 最基本和常见的Redis数据类型
- Redis字符串存储字节序列
- 最大大小为512 MB

```other
SET | GET | DEL
INCR | INCRBY | INCRBYFLOAT
MSET | MGET

注意：DEL是阻塞的，而UNLINK是非阻塞的
```

## List（列表）

![1*-DoC-iO1T8qRKUgd9mEG5A.png](https://miro.medium.com/v2/resize:fit:1400/1*-DoC-iO1T8qRKUgd9mEG5A.png)

- 字符串值的链表
- 元素的集合
- 每个元素都是一个字符串
- 可以包含超过40亿个元素
- 元素顺序基于插入顺序
- 编码和内存优化

```other
LPUSH | LRANGE | RPUSH | LPOP | RPOP | LLEN
LTRIM | LINDEX | LINSERT | LSET | LPOS | LREM
```

## Set（集合）

- Redis集合是一组无序的唯一字符串（成员），用于跟踪唯一元素。

```other
SADD | SREM | SCARD | SMEMBERS | SDIFF | SDIFFSTORE
SISMEMBER | SMISMEMBER | SMOVE | SPOP
```

## Hash（哈希）

![1*nBB3vFuBYooNrPuS-Fnz9Q.png](https://miro.medium.com/v2/resize:fit:1400/1*nBB3vFuBYooNrPuS-Fnz9Q.png)

- Redis哈希是作为字段-值对集合结构化的记录类型。每个哈希可以存储多达42亿个（2^32-1）个字段-值对。哈希可用于在应用程序中存储会话和个人资料。

```other
HSET | HGET | HGETALL | HDEL | HEXIST | HMSET | HMGET
```

## Sorted Set（有序集合）

![1*CkISVoBW8dIyC4YOu9005Q.png](https://miro.medium.com/v2/resize:fit:1310/1*CkISVoBW8dIyC4YOu9005Q.png)

- Redis有序集合是一组唯一字符串（成员），按相关分数进行排序的集

合。它可以用于排行榜、优先级队列、二级索引和速率限制器。

```other
ZADD | ZCARD | ZSCORE | ZRANK | ZREVRANK | ZREM | ZRANGE
```

## HyperLogLog

> HyperLogLog是一种用于估计集合基数的数据结构。它可用于跟踪唯一访问者数量。HyperLogLog的实现使用了多达12KB的空间。
PFADD | PFCOUNT | PFMERGE

## Streams

![1*8NRb5TeJ34Jf08do8OzGxg.png](https://miro.medium.com/v2/resize:fit:1400/1*8NRb5TeJ34Jf08do8OzGxg.png)

Redis流是一种类似追加日志的数据结构。您可以使用流记录和同时传播实时事件。

XADD | XREAD | XTRIM | XDEL

XGROUP CREATE | XGROUP DESTROY | XREADGROUP

XGROUP CREATECONSUMER | XGROUP DELCONSUMER

Redis Streams允许At-most-once或At-least-once的消息传递。Redis Streams支持同步和异步读取。

## Bitmap

Redis位图是字符串数据类型的扩展，它允许您将字符串视为位向量。

BITOP | BITPOS | BITCOUNT

![1*QZ7buW1SRfDIeccgmiXxGw.png](https://miro.medium.com/v2/resize:fit:1400/1*QZ7buW1SRfDIeccgmiXxGw.png)

图片来源 — [bytebytego.com](http://bytebytego.com)

## Bitfield

Redis位字段允许您设置、增加和获取任意位长度的整数值。

## Geospatial

> Redis地理空间索引允许您存储坐标并进行搜索，查找给定半径或边界框内的附近点。
GEOADD | GEODIST | GEORADIUS | GEOSEARCH | GEOPOS

- *Redis使用Haversine公式计算距离。*
- *Redis将地理空间点存储在有序集合中。集合的分数用于编码坐标对。*

## Key Expiration

![1*tf8z_SgKoz69y4sXtpwIxg.png](https://miro.medium.com/v2/resize:fit:1400/1*tf8z_SgKoz69y4sXtpwIxg.png)

> EX — 指定秒数后过期

> PX — 指定毫秒数后过期

> EXAT — 指定时间戳后过期（秒）

> PXAT — 指定时间戳后过期（毫秒）

> TTL — 键的剩余生存时间（近似值）

> PERSIST — 移除当前的过期时间

## Pipelining

Redis遵循客户端-服务器模型。客户端发送请求，服务器处理请求并返回响应。吞吐量主要由往返时间（通常在毫秒级别，请求处理通常在微秒级别）决定。Redis流水线是一种提高性能的技术，它可以一次性发出多个命令，而无需等待每个单独命令的响应。这是一种网络优化，因为我们减少了客户端和服务器之间的多次往返。

![Image.tiff](https://res.craft.do/user/full/b313a1ab-3121-84d1-2506-d702b5c0ba9c/doc/7DEE7354-305E-4372-A775-D871034E4CA3/FC223F37-4E85-4EE0-9E7A-6DEC9FDCCC72_2/eoba36BzrySg2QA4b1hf6GnSaxP4vtjxsH6jqwuZN7gz/Image.tiff)

*注意：1. 管道不保证命令执行顺序。2. 在管道中不允许在写操作之后进行读操作，因为所有命令的结果将在最后一起返回。3. 在进行管道操作时，需要使用哈希标记（hash-tags），以便将键强制映射到同一个分片上。4. 管道不是原子性的。5. Python库中的管道被包装在MULTI/EXEC中。*

## Scripting

调用服务器端Lua脚本的执行。

SCRIPT LOAD | FUNCTION LOAD | EVAL | FCALL | FUNCTION KILL | SCRIPT KILL

*注意：Lua脚本是原子的且是阻塞的。*

## Pub/Sub

Redis通过发布/订阅机制提供了一种解耦的消息传递范式。发布者将消息发送到频道，订阅者确认对一个或多个频道感兴趣，并接收感兴趣的消息。

![1*tV8vZ9MMnYPok7PiTKKjyQ.png](https://miro.medium.com/v2/resize:fit:1400/1*tV8vZ9MMnYPok7PiTKKjyQ.png)

SUBSCRIBE | PUBLISH | PUBSUB CHANNELS | UNSUBSCRIBE

*注意：发布/订阅与键空间无关。*

## 事务

Redis事务允许将一组命令作为单个步骤执行，它们围绕以下命令展开：

> MULTI — 事务的开始

> EXEC — 事务的结束

> DISCARD — 取消事务

> WATCH — 持续监视键是否在我们开始监视后被修改

![1*mLHM_sFayUdEoT3AOKs5tg.png](https://miro.medium.com/v2/resize:fit:1400/1*mLHM_sFayUdEoT3AOKs5tg.png)

> UNWATCH
事务中的所有命令都是串行化执行的。在Redis事务的执行过程中，不会在执行Redis事务的过程中为另一个客户端服务。

## 持久化

在Redis中，如果启用持久化，数据会被持久化到磁盘。重新启动时，它会将数据加载到内存中进行计算。

![1*r497z2Kjtg3iEa12LmDHsw.png](https://miro.medium.com/v2/resize:fit:1400/1*r497z2Kjtg3iEa12LmDHsw.png)

> RDB — 在特定间隔的特定时间点进行快照AOF — 记录每个写操作的日志，每秒同步一次，后台线程，在恢复时回放日志
注意：您可以通过发出BGSAVE命令手动进行快照。

