---
layout: post
title: "缓存：系统设计中至关重要的一环"
categories: 系统设计
tags: 缓存技术
keywords: 缓存技术, 缓存策略, 缓存旁路, 写入旁路, 读取穿透缓存, 写入穿透缓存, 写入后缓存,数据访问模式优化, 系统性能优化
date: 2023/12/17 20:34:25
---


![1*miHYi3-erQzdPaF0arbxwQ.png](https://miro.medium.com/v2/resize:fit:2000/1*miHYi3-erQzdPaF0arbxwQ.png)

## 什么是缓存？

缓存就像是一个超快速的存储区域，保存了计算机或手机经常使用的内容的副本，这样可以在不访问较慢的主存储器的情况下快速获取。

一个**现实中的例子**可以是，每当我们购买杂货时，通常会倾向于大量购买，这样可以让杂货多存放一段时间，避免频繁去市场购买，这其实就是将杂货缓存在我们附近，而不是每次都从市场购买。

在系统设计中，如果缓存得当，它可以显著提升系统的性能。

缓存策略取决于**数据访问模式**，即数据是如何读取或写入的。例如：

- 系统是读取密集型还是写入密集型？
- 系统是否需要高一致性？

等等……

因此，选择正确的写入缓存策略非常关键，下面是一些不同的**缓存策略**：

<!--more-->

# 1. 缓存旁路（懒加载）

在这种设置中，应用程序**缓存被分离**出来，应用程序显式地与缓存和数据库一起工作。这是一种技术，应用程序代码负责管理缓存。当需要时，应用程序会将数据显式加载到缓存中，而缓存不会主动参与数据获取。

![1*kf4ytjqSKx-UAYaA-MW4Hw.png](/uploads/1*kf4ytjqSKx-UAYaA-MW4Hw.png)

缓存旁路

该图示了其工作原理。

## **使用场景：**

适用于读取密集型系统，Redis 或 Memcached 非常受欢迎，我曾经在缓存旁路设置中使用过 Redis 以及 Mongo-db，效果非常显著。

这种读取技术可以与诸如**写入旁路缓存**之类的数据写入技术相结合，我接下来会解释。

## **优点：**

- **灵活性**：允许选择性缓存特定数据。
- **控制**：应用程序控制数据何时加载到缓存中。

## **缺点：**

- **提供过期数据**：可能会提供过期数据，但如果我们实现了缓存的TTL（生存时间），则可以避免这种情况。

# 2. 写入旁路

跳过缓存，直接将数据写入数据库，并在读取用户请求的数据时更新缓存。

## **使用场景：**

写入旁路可以与读取通过结合，对于数据写入一次，读取频率较低或几乎不读取的情况下提供良好的性能，例如实时日志或聊天室消息。同样，这种模式也可以与缓存旁路结合使用。

# 3. 读取穿透缓存

读取穿透缓存是一种策略，当发生**缓存未命中**时，缓存会自动从底层数据源检索数据并填充自身。这种技术与应用程序的数据访问层无缝集成，确保缓存与数据源保持同步。

![1*h-CUV05gwKCUl7GdFfS_Nw.png](/uploads/1*h-CUV05gwKCUl7GdFfS_Nw.png)

读取穿透缓存

## **使用场景、优点和缺点：**

读取穿透缓存适用于**读取密集**的工作负载，当同一数据被多次请求时。例如，一个新博客。缺点是，当首次请求数据时，总是会导致缓存未命中，并造成额外的数据加载开销。

# 4. 写入穿透缓存

写入穿透缓存是一种策略，其中写操作同时应用于**缓存**和底层**数据源**。这确保了缓存和数据源保持同步，但与写入后缓存相比可能会引入额外的延迟。它同步应用更新。

![1*7TjoMj10e--091ttHEKvTg.png](/uploads/1*7TjoMj10e--091ttHEKvTg.png)

## **使用场景、优点和缺点：**

当与读取穿透缓存结合时，写入穿透缓存可以保证每次读取和写入的数据一致性。但它会增加写操作的额外开销，因为每次写入都需要两次写入操作（缓存和数据库）。它以异步方式应用更新。

# 5. 写入后缓存

写入后缓存，也称为**写回缓存**，涉及在写操作发生时延迟对数据源的更新。系统不会立即更新底层存储，而是首先更新缓存，然后异步将更改传播到数据源。

![1*Sfn9HMtmSggo3FCQIyxJPQ.png](/uploads/1*Sfn9HMtmSggo3FCQIyxJPQ.png)

## **使用场景、优点和缺点：**

写回缓存提高了写入性能，非常适用于写入密集型任务。当与读取穿透结合时，适用于混合工作负载，确保最近的

数据可用。

# 总结：

本文探讨了缓存技术，强调了根据数据访问模式选择正确策略的重要性。了解这些缓存策略对于在不同场景中优化系统性能至关重要。



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
