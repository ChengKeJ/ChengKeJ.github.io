---
layout: post
title:  "删除是不是只能跑路？"
categories: mysql
tags:  mysql
date: 2021/11/01 20:46:25
---


##### 数据恢复过程

1.最近的一次全量备份 -> 2. 从备份的时间点开始,将备份的binlog 依次取出来重放到误删表之前的那个时刻

这样你的临时库就跟误删之前的线上库一样了,按需要恢复到线上库去。

##### 日志模块

与查询流程不一样的是，更新流程还涉及两个重要的日志模块

redo log 是 InnoDB 引擎特有的日志，而 Server 层也有自己的日志，称为 binlog（归档日志）

<!--more-->

###### redo log

WAL 的全称是 Write-Ahead Logging，它的关键点就是先写日志，再写磁盘，也就是先写粉板，等不忙的时候再

写账本。

具体来说，当有一条记录需要更新的时候，InnoDB 引擎就会先把记录写到 redo log（粉板）里面，并更新内存，

这个时候更新就算完成了。同时，InnoDB 引擎会在适当的时候，将这个操作记录更新到磁盘里面，而这个更新往

往是在系统比较空闲的时候做，这就像打烊以后掌柜做的事

有了 redo log，InnoDB 就可以保证即使数据库发生异常重启，之前提交的记录都不会丢失，这个能力称为 crash-safe。

###### binlog

redo log 是 InnoDB 引擎特有的日志，而 Server 层也有自己的日志，称为 binlog（归档日志）

###### redo log vs binlog

* redolog 是InnoDB特有,binlog 是mysql server 层独有

* redo log 是物理日志，记录的是“在某个数据页上做了什么修改”；binlog 是逻辑日志，记录的是这个语句的原

  始逻辑，比如“给 ID=2 这一行的 c 字段加 1 ”。

* redo log 是循环写的，空间固定会用完；binlog 是可以追加写入的。“追加写”是指 binlog 文件写到一定大小

  后会切换到下一个，并不会覆盖以前的日志

##### 两阶段提交

什么是”两阶段提交“?

先看数据update的mysql执行过程如图, 浅色 InnoDB 内部执行的，深色 执行器中执行的

<img src="https://cdn.jsdelivr.net/gh/ChengKeJ/pic@master/img/2e5bff4910ec189fe1ee6e2ecc7b4bbe.png" alt="img" style="zoom:50%;" />

Innodb更新完内存然后写入redo log -> prepare状态 -> 告知执行器执行完成了，随时可以提交事务。执行器生成

这个操作的 binlog，并把 binlog 写入磁盘 ->更新redo log状态为commit的

为什么必须有“两阶段提交”呢？

这是为了让两份日志之间的逻辑一致.

比如在更新的时候, 如果redo log 完好,意味中数据库重启 crash了 当时数据库数据可以恢复正常,但是归档的数据

会少一条更新的语句.如果后续误删数据利用备份库+binlog进行数据恢复的时候,会少一条更新语句.

反之亦然,binlog完好,但是redo log异常,恢复之后的数据认识更新前的值,后续binlog 恢复出来的值与原值不同.

redo log 和 binlog 都可以用于表示事务的提交状态，而两阶段提交就是让这两个状态保持逻辑上的一致.

























