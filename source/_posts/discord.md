---
layout: post
title:  "Discord如何存储数万亿条消息"
categories: 设计
tags:  设计
date: 2023/04/01 14:46:25
---


Discord是一款专为社群设计的免费网络实时通话软件与数字发行平台，主要针对游戏玩家、教育人士、朋友及商业人士，用户之间可以在软体的聊天频道通过消息、图片、视频和音频进行交流。这款软件可以在Microsoft Windows、macOS、Android、iOS、Linux和网页上运行。

下面的图展示了Discord消息存储的演变历程：

![67b3962d-becb-4fba-a9ae-115692dfa6e1_3576x3033.jpeg](https://res.craft.do/user/full/8cf3a6c7-2a00-8ee8-0195-0eb38fda2eeb/5915084F-1F12-445D-B6F4-BE12D63C75AE_2/y6PCZxe8EwmpdNacqIRuPwKScgJthxcgJyg1eKOEYdsz/67b3962d-becb-4fba-a9ae-115692dfa6e1_3576x3033.jpeg)

<!--more-->

MongoDB ➡️ Cassandra ➡️ ScyllaDB

2015年，Discord的第一个版本建立在一个单独的MongoDB副本之上。到了2015年11月左右，MongoDB存储了1亿条消息，但是RAM已经无法再容纳数据和索引了。此时延迟变得不可预测。因此，需要将消息存储转移到另一个数据库。最终选择了Cassandra。

2017年，Discord有12个Cassandra节点，并存储了数十亿条消息。

到2022年初，它已经有177个节点，并存储了数万亿条消息。此时，延迟变得不可预测，并且维护操作的成本变得太高。

问题的原因有以下几点：

- Cassandra使用LSM树作为内部数据结构。读操作比写操作更昂贵。在具有数百个用户的服务器上可能有很多并发读取，从而导致热点问题。
- 维护集群（如压缩SSTables）会影响性能。
- 垃圾回收暂停会导致显著的延迟峰值。

ScyllaDB是一个兼容Cassandra的数据库，用C++编写。Discord重新设计了其架构，具有单片API、用Rust编写的数据服务和基于ScyllaDB的存储。

在ScyllaDB中，p99读取延迟为15毫秒，而Cassandra中为40-125毫秒。p99写入延迟为5毫秒，而Cassandra中为5-70毫秒。

ScyllaDB是一个快速、可扩展和高可用的分布式数据库，适用于大规模数据处理和高吞吐量应用程序。它已被广泛应用于云计算、物联网、金融服务、在线广告和游戏等领域，被视为具有很高性价比的数据库解决方案。

以下是一个使用 Java 驱动程序连接 ScyllaDB 并执行查询的示例

```other
import com.datastax.driver.core.*;

public class ScyllaDBExample {
  public static void main(String[] args) {
    // Connect to the cluster and keyspace
    Cluster cluster = Cluster.builder().addContactPoint("127.0.0.1").build();
    Session session = cluster.connect("test");

    // Execute a simple query
    ResultSet results = session.execute("SELECT * FROM users");

    // Print the results
    for (Row row : results) {
      System.out.format("%d %s %s\n", row.getInt("id"), row.getString("name"), row.getString("email"));
    }

    // Close the session and cluster
    session.close();
    cluster.close();
  }
}
```

这个例子使用 DataStax Java 驱动程序连接 ScyllaDB 并执行一个简单的查询来获取所有用户的记录。它打印查询结果，然后关闭会话和集群。

要运行这个示例，你需要将 DataStax Java 驱动程序添加到你的 Java 项目中。可以通过 Maven 或 Gradle 添加以下依赖项：

```other
<dependency>
  <groupId>com.datastax.cassandra</groupId>
  <artifactId>cassandra-driver-core</artifactId>
  <version>4.14.0</version>
</dependency>
```

在这个示例中，我们使用了默认的本地节点 IP 地址 127.0.0.1，也可以使用 ScyllaDB 集群中的其他节点 IP 地址。在实际应用中，建议使用连接池和预处理语句来提高性能。

参考资料：

[scylladb.com/product/technology/shard-per-core-architecture/](http://scylladb.com/product/technology/shard-per-core-architecture/)

[discord.com/blog/how-discord-stores-trillions-of-messages](http://discord.com/blog/how-discord-stores-trillions-of-messages)

