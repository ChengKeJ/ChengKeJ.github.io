---
layout: post
title:  "Nagle和延迟确认带来的性能问题"
categories: network
tags:  tcp
keywords: Nagle,延迟确认,性能问题,tcp
date: 2022/12/23 23:43:25
---

# Nagle和延迟确认带来的性能问题

### 前言

从网上看到的一个问题的跟踪@林沛满 ，觉得很有意思记录下来

### 问题背景

从AIX备份数据到Windows极其缓慢，只有1MB/s，备份所用的协议是SFTP，看到这个问题第一反应抓个包，试试Wireshark的三板斧

### 抓包三板斧

1. 从Statistics →Summary菜单可见，平均速度是11 Mbit/s，的确只比1 MB/s高一些

<!--more-->

![Image.png](https://res.craft.do/user/full/d3f184bd-2fc6-ae80-c9ed-d9b26e0cd7ba/doc/0DEEB625-00C3-41BD-B624-683B699924E8/EF3B1138-D940-4964-B487-C60AC4E5D08F_2/EtZkFgVqobsgX247lVw9nQzEXGuQWP3e8G4Qyyzmwh0z/Image.png)



2. 从Analyze →Expert Infos菜单看，网络状况堪称完美。连一个Warnings和Notes都没有。这样的网络性能怎么会差？

![Image.png](https://res.craft.do/user/full/d3f184bd-2fc6-ae80-c9ed-d9b26e0cd7ba/doc/0DEEB625-00C3-41BD-B624-683B699924E8/0A35D2DB-D49E-47D3-9A85-5030507663E0_2/WweckqTyOemsxBQ7nxDxF33Pph8lDpQPhwWfKe48Nb0z/Image.png)

3．选定一个从AIX发往Windows的包，然后点击Statistics→TCP StreamGraph→TCP Sequence Graph（Stevens）菜单，从图3可见，这60秒中数据传输得很均匀，没有TCP Zero Window或者死机导致的暂停。

![Image.png](https://res.craft.do/user/full/d3f184bd-2fc6-ae80-c9ed-d9b26e0cd7ba/doc/0DEEB625-00C3-41BD-B624-683B699924E8/AF93E34F-32EF-4367-84A7-FA0CAC47D1E2_2/rMHgascgFBacaJhygVrPR4ut4KYUHQzMUjuxylt600wz/Image.png)

试完三板斧，我们只能得到一个结论：备份的确进行得很慢，但是仅凭Wireshark自带的分析工具找不出根本原因，这也许意味着问题不在网络上，而是在接收方或者发送方上。幸好**Wireshark不但能发现网络上的问题，也能反映出接收方和发送方的一些配置，只是需要一些技巧来发现**。

因为数据是从AIX备份到Windows的，所以如果把SFTP（SSH File Transfer Protocol）包过滤出来，理论上应该看到大多数时间花在了从AIX到Windows的传输上。可是由图4发现，从AIX到Windows的包虽然占多数，但没花多少时间。反而从Windows到AIX的两个包（533和535）之间竟然隔了0.2秒。该现象在整个传输过程中出现得很频繁，说不定性能差的原因就在此处了。只要把根本原因找出来，就有希望解决问题。

![Image.png](https://res.craft.do/user/full/d3f184bd-2fc6-ae80-c9ed-d9b26e0cd7ba/doc/0DEEB625-00C3-41BD-B624-683B699924E8/AE3CDB44-D330-47AC-92D3-32D6CA9DD39A_2/gCQjzWaAFAnEdgrLQEO0HcQ3LV9TaNU6EVLKTbPl6Sgz/Image.png)

那么这0.2秒之间究竟发生了什么呢？我把过滤条件去掉后得到了图5所示的包。可见Windows发出533号包之后就停下来等，直到0.2秒之后AIX的Ack（534号包）到达了才发出535号。Windows停下来的原因是什么呢？它在这两个包里总共才发了700多字节（96+656）的数据，肯定不会是因为TCP窗口受约束所致。

![Image.png](https://res.craft.do/user/full/d3f184bd-2fc6-ae80-c9ed-d9b26e0cd7ba/doc/0DEEB625-00C3-41BD-B624-683B699924E8/DC666B15-A553-4A1F-BEC2-610F6BE84D33_2/du6xMWrJDCw4SIgAHtWkNhxiVNXUtTJwp8fwCjPG1qkz/Image.png)

如果你研究过TCP协议，可能已经想到了愚笨窗口综合症（Silly window syndrome）和纳格（Nagle）算法。在某些情况下，应用层传递给TCP层的数据量很小，比如在SSH客户端以一般速度打字时，几乎是逐个字节传递到TCP层的。传输这么少的数据量却要耗费20字节IP头+20字节TCP头，是非常浪费的，这种情况称为发送方的愚笨窗口综合症，也叫“小包问题”（small packet problem）。为了提高传输效率，纳格提出了一个算法，用程序员喜闻乐见的方式表达就是这样的：

```other
if有新数据要发送
  if数据量超过MSS（即一个TCP包所能携带的最大数据量）
    立即发送
  else
    if之前发出去的数据尚未确认
      把新数据缓存起来，凑够MSS或等确认到达再发送
    else
      立即发送
    end if
  end if
end if
```

所示的状况恰好就是小包问题。Windows发了533号包之后，本应该立即发送535的，但是由于535携带的数据量小于MSS，是个小包，根据Nagle算法只好等到533被确认（即收到534）才能接着发。这一等就是0.2秒，所以性能受到了大幅度影响。那为什么AIX要等那么久才确认呢？因为它启用**延迟确认**了。

### 原因定位

Nagle和延迟确认本身都没有问题，但一起用就会影响性能。解决方法很简单，要么在Windows上关闭Nagle算法，要么在AIX上关闭延迟确认。这位客户最终选择了前者，性能立即提升了20倍。如果你足够细心，也许已经意识到图3有问题——既然传输过程中会频繁地停顿0.2秒，为什么图3显示数据传输很均匀呢？这是因为抓包时间太长了，有60秒，所以0.2秒的停顿在图上看不出来。假如只截取其中的一秒钟来分析，再点击Statistics →TCP StreamGraph→TCP Sequence Graph（Stevens）菜单，你就能看到图6的结果，0.2秒的停顿就很明显了

![Image.png](https://res.craft.do/user/full/d3f184bd-2fc6-ae80-c9ed-d9b26e0cd7ba/doc/0DEEB625-00C3-41BD-B624-683B699924E8/7B35CAAB-7898-4F0A-AACA-8B750ED9BF16_2/IHeN7rWRRA6hwp37slXx3R9gZ1arZodWDbC3Dx5QjYYz/Image.png)

文章到尾声，按理说这种情况，世界到处都有同时启用Nagle和延迟确认的设置在通信，那为什么很少有人发现这个问题呢？估计大部分人都以为是带宽不足吧。

当然网上也有类型的分享，感兴趣的可以看看

[https://www.diglog.com/story/1036249.html](https://www.diglog.com/story/1036249.html)

[http://www.stuartcheshire.org/papers/nagledelayedack/](http://www.stuartcheshire.org/papers/nagledelayedack/)




