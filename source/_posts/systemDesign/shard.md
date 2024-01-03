---
layout: post
title: "分片并不意味着分布式"
categories: 数据库分片
tags: 系统设计,数据库分片,分片技术
keywords: 数据库分片, 分布式数据库, 分片技术, 数据库水平扩展, 数据分布策略, 数据库架构比较, 分布式数据管理, 数据库水平可扩展性, 分布式系统, 数据节点管理
date: 2023/12/03 14:53:25
---

Sharding（分片）是一种将数据和负载分布到多个独立的数据库实例的技术。这种方法通过将原始数据集分割为分片来利用水平可扩展性，然后将这些分片分布到多个数据库实例中。

![1*yg3PV8O2RO4YegyiYeiItA.png](/uploads/1*yg3PV8O2RO4YegyiYeiItA.png)

但是，尽管"分布"一词出现在分片的定义中，但分片数据库并不是分布式数据库。

<!--more-->

## 分片解决方案

每个分片解决方案在其架构中都有一个关键组件。该组件可以有各种名称，包括协调器、路由器或导演：

![1*kp39_8mQ0E9bIO0Lw3PGFw.png](/uploads/1*kp39_8mQ0E9bIO0Lw3PGFw.png)

协调器是唯一一个知道数据分布的组件。它将客户端请求映射到特定的分片，然后转发到相应的数据库实例。这就是为什么客户端必须始终通过协调器路由其请求的原因。

例如，如果客户端想要将新记录插入到`Car`表中，请求首先会传递到协调器。协调器将记录的主键映射到其中一个分片，然后将请求转发到负责该分片的数据库实例。

![1*YNUB6y8WJnp0CCVAXSjQ0g.png](/uploads/1*YNUB6y8WJnp0CCVAXSjQ0g.png)

在上面的示意图中，首先，协调器将键`121`映射到分片`10`，然后将记录插入到存储在拥有分片`10`的数据库实例上的表`car_10`中。

然而，还有一个问题：为什么在分片解决方案中需要协调器呢？答案很直接。分片存储在设计用于单服务器部署的数据库实例上。

*这些数据库实例不相互通信，也不支持任何能促进这种通信的协议。它们彼此不知道，存在于各自的隔离环境中，对于它们是一个更大系统的一部分这一事实毫不知情。*

因此，在分片解决方案中，协调器是不可或缺的。如果您有兴趣更深入地了解分片数据库架构，请考虑探索用于PostgreSQL的CitusData或Azure CosmosDB，用于MySQL的Vitess，用于Oracle的Distributed Autonomous Database以及MongoDB Sharded Cluster。

## 分布式数据库

与分片数据库解决方案类似，分布式数据库也使用类似的分片技术在数据库节点群集中分发数据和负载。但是，与分片解决方案不同，分布式数据库不依赖于协调器组件。

**分布式数据库建立在共享无关架构上**，没有像协调器这样的单一组件负担着做出许多决策：

![1*deOgcXccWs9lKUSgLPNOww.png](/uploads/1*deOgcXccWs9lKUSgLPNOww.png)

*集群中的所有节点都知道对方，因此也知道数据的分布。通过直接通信，每个节点可以将客户端请求路由到适当的分片所有者。此外，它们可以执行和协调多节点事务。当扩展到更多节点时，集群会自动重新平衡和分割分片。节点保持数据的冗余副本（基于配置的复制因子），即使某些节点失败，也可以继续操作而无需停机。*

所有这些对于客户端来说是透明的，客户端只需与任何节点建立连接，然后允许该节点管理分布式方面。

例如，客户端可能连接到`node1`并插入具有id`121`的新的`Car`记录。如果`node1`是记录分片的所有者，则它将在本地存储记录，并使用共识算法将更改复制到其他节点的子集。如果不是，`node1`将记录转发到分片的所有者，可能是`node4`。

![1*weEdq2BxIpf6GiLjipns5Q.png](/uploads/1*weEdq2BxIpf6GiLjipns5Q.png)

如果您有兴趣探索真正分布式数据库的架构，请考虑研究Google Spanner，YugabyteDB，CockroachDB，Apache Cassandra或Apache Ignite。

在数据库领域，分片和分布经常被混为一谈，但它们有着不同的目的。

虽然分片涉及将数据分割到多个独立的实例中，但这并不意味着系统本质上是分布式的。分片解决方案中协调器的存在，该协调器指导客户端请求到适当的分片，突显了这一区别。

另一方面，建立在共享无关架构上的分布式数据库缺乏这种集中式协调器。这些系统中的节点都知道对方，管理数据分布，并无缝处理客户端请求。

这两种架构都有其优点，了解它们的细微差别对于进行数据库设计和选择至关重要。


## 系统设计概念系列文章

[计算机的层次化架构](http://mp.weixin.qq.com/s?__biz=MzU0NDMzMjY5Ng==&mid=2247488478&idx=1&sn=351b322c2089d8fd7124cf801e0a0524&chksm=fb7c9d29cc0b143facd8d62fb15812d6d28e22749e1183c48d6946dbd13d2e206cd9826bfb20&scene=21#wechat_redirect)

[每个开发者都应该知道的7个原则](http://mp.weixin.qq.com/s?__biz=MzU0NDMzMjY5Ng==&mid=2247488411&idx=1&sn=2520f364a171f0d747a855600c99cc4b&chksm=fb7c9d6ccc0b147a44627a8eef07b5772acbda406ce566afe306ed2b3c98f665e05c12b6ad36&scene=21#wechat_redirect)

[6个系统设计的基本概念](http://mp.weixin.qq.com/s?__biz=MzU0NDMzMjY5Ng==&mid=2247488409&idx=1&sn=a02b97d71d301be4200ab25e4ffad0bc&chksm=fb7c9d6ecc0b1478f85986554735e307e2c92d45236f241dec44a529e9c799c521d5448f9f9a&scene=21#wechat_redirect)

[数据库：系统设计的核心](http://mp.weixin.qq.com/s?__biz=MzU0NDMzMjY5Ng==&mid=2247488407&idx=1&sn=6072efba48ed2f9f1968594a1055a3f6&chksm=fb7c9d60cc0b14766e394d5c04f33d5b1c63987d6cf5b66732887e9ffedbbfc576e5a6617891&scene=21#wechat_redirect)


## 图解系列

[系统设计中的缓存技术：完整指南](http://mp.weixin.qq.com/s?__biz=MzU0NDMzMjY5Ng==&mid=2247488260&idx=1&sn=89701a38ab8af1c670b432b5e7bedf2d&chksm=fb7c9df3cc0b14e52a043999e59b5c1251c3183a61b8c9d35777f237d1478c07b7d01352346e&scene=21#wechat_redirect)

[关系数据库的全景图](http://mp.weixin.qq.com/s?__biz=MzU0NDMzMjY5Ng==&mid=2247488165&idx=1&sn=2c4aaad53e6ca87f61e23dfba2fd8f9e&chksm=fb7c9c52cc0b1544266bb7e008f55bfbaa9b2acdb512eb07beece554e9f766e80f12699208b7&scene=21#wechat_redirect)

[Redis 全景解析](http://mp.weixin.qq.com/s?__biz=MzU0NDMzMjY5Ng==&mid=2247487961&idx=1&sn=f1437b130e1828f4c1c0c45806e4922f&chksm=fb7c9f2ecc0b1638cf34322599d4bd42c002bf2e37c642d9dbc596366f91f237f410b4e90fc5&scene=21#wechat_redirect)

当然架构设计、全景图解系列还有很多,快来关注一起学习吧~
