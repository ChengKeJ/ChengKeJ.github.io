---
layout: post
title: "计算机的层次化架构"
categories: 系统设计
tags: 系统设计,计算机层次结构,硬件优化
keywords: 比特, 字节, 存储设备, SSD, HDD, RAM, DDR4, LPDDR5, 缓存存储, CPU, 主板,SSD vs. HDD, RAM速度, RAM容量, CPU执行, 主板功能,存储设备速度, RAM性能, 缓存存储对CPU的影响, 主板连接,计算机层次结构, 硬件优化, 数据处理, 系统集成
date: 2023/12/03 16:53:25
---

计算机在多层次系统中运行。每个层次都经过优化，以执行特定的任务，确保整个机器的高效运行。

![1*1CjGIK6LMua2woczwH1r6A.png](/uploads/1*1CjGIK6LMua2woczwH1r6A.png)

## 从比特到大字节：理解计算机内存单元

在最基本的层面上，计算机使用二进制运行，只包含 0 和 1，被称为**比特**。以下是一个细分：

**1 比特：** 这是计算机中的基本数据单元，表示为 0 或 1。

**1 字节：** 包含 8 个比特，字节用于表示单个字符（例如，“A”）或数字（例如，1）。

<!--more-->

![1*jwxOxDwjT18FwRsqsVztCg.png](/uploads/1*jwxOxDwjT18FwRsqsVztCg.png)

进一步扩展：

- 千字节（KB）：大约 1,024 字节。
- 兆字节（MB）：大约 1,024 KB。
- 吉字节（GB）：大约 1,024 MB。
- 太字节（TB）：大约 1,024 GB。

## 硬盘存储：数据的中央存储库

计算机的主要数据存储在硬盘存储设备中。

示例：这包括固态驱动器（SSD）和硬盘驱动器（HDD）。

![1*jlQhFPO24uzLfAoGV_rETw.png](/uploads/1*jlQhFPO24uzLfAoGV_rETw.png)

显著特点：

- **非易失性：** 即使关闭电源或重新启动计算机，数据仍然保持完好。
- **存储空间：** 硬盘存储设备的容量通常在 100 GB 到几 TB 之间。
- **速度差异：** 尽管价格较高，但相较于 HDD，SSD 的数据检索速度显著更快。例如，SSD 的读取速度可能在 500 MB/s 到 3,500 MB/s 之间（取决于类型和质量），而 HDD 可能提供 80 MB/s 到 160 MB/s。

## RAM：数据处理的前沿

随机访问存储器（RAM）充当正在使用或正在处理的数据的主要持有者。当启动程序时，由于其快速的读写能力，程序的数据 — 从变量到运行时堆栈 — 存储在这里。

示例：常见的 RAM 类型包括 DDR4 和 LPDDR5。

![1*HgYTk5q2B9dkeYlpg_T6WA.png](/uploads/1*HgYTk5q2B9dkeYlpg_T6WA.png)

关键点：

- **易失性内存：** 断电时 RAM 的内容会丢失。
- **速度：** RAM 比硬盘存储快得多，通常超过 5,000 MB/s。
- **存储范围：** 对于典型的消费者设备，RAM 的范围可以从几 GB（8 GB、16 GB、32 GB）到高端服务器可以拥有数百 GB（128 GB 或更多）。

## 缓存存储：速度大师

即使迅速的 RAM 不足以满足需求时，还有缓存存储。

![1*W25HWBrg7ku2qh9qoptveQ.png](/uploads/1*W25HWBrg7ku2qh9qoptveQ.png)

特征：

- **尺寸：** 缓存比 RAM 小但比 RAM 更快，通常以兆字节为单位。
- **速度：** 它的访问时间超过 RAM，使其成为存储频繁使用的数据以实现最佳 CPU 性能的理想选择。

## CPU：每台计算机的心跳

中央处理单元（CPU）通常被称为计算机的“大脑”。它在提取、解码和执行指令方面发挥着关键作用。

![1*Gs4u9u7X6rV4LUi92YC7TQ.png](/uploads/1*Gs4u9u7X6rV4LUi92YC7TQ.png)

特征：

- **执行：** CPU 解释和处理来自我们代码的操作。
- **代码翻译：** 高级编程语言需要被翻译成机器代码 — 一系列 0 和 1 — 以便 CPU 理解和运行它们。这种转换是由编译器执行的。

![1*IpOltycnP34DNe_3Uz8T8A.png](/uploads/1*IpOltycnP34DNe_3Uz8T8A.png)

## 主板：组

件的统一者

最后但并非最不重要，主板（或主板）充当中央枢纽，连接上述所有组件。从托管 CPU 和 RAM 插槽到为数据流提供路径，主板确保所有计算机组件的无缝集成和功能。

![1*MJaF3KQr2bDU_wfsE9vNCg.png](/uploads/1*MJaF3KQr2bDU_wfsE9vNCg.png)

## 总结

通过对这些基本概念的理解，您更好地准备深入探讨系统设计的领域。

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
