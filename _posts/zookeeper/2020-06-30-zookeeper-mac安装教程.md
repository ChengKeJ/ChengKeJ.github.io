---
layout: post
title:  "zookeeper 安装教程"
categories: zookeeper
tags:  zookeeper 安装教程
---

### brew安装

```$xslt
brew install zookeeper
```

```$xslt
==> Downloading https://homebrew.bintray.com/bottles/zookeeper-3.4.13.mojave.bottle.tar.gz
==> Downloading from https://akamai.bintray.com/d1/d1e4e7738cd147dceb3d91b32480c20ac5da27d129905f336ba51c0c01b8a476?__gd
######################################################################## 100.0%
==> Pouring zookeeper-3.4.13.mojave.bottle.tar.gz
==> Caveats
To have launchd start zookeeper now and restart at login:
  brew services start zookeeper
Or, if you don't want/need a background service you can just run:
  zkServer start
==> Summary
🍺  /usr/local/Cellar/zookeeper/3.4.13: 244 files, 33.4MB
```
查看zookeeper 信息

```$xslt
brew info zookeeper
```
```$xslt
zookeeper: stable 3.4.13 (bottled), HEAD
Centralized server for distributed coordination of services
https://zookeeper.apache.org/
/usr/local/Cellar/zookeeper/3.4.13 (244 files, 33.4MB) *
  Poured from bottle on 2020-06-30 at 17:51:47
From: https://github.com/Homebrew/homebrew-core/blob/master/Formula/zookeeper.rb
==> Options
--HEAD
	Install HEAD version
==> Caveats
To have launchd start zookeeper now and restart at login:
  brew services start zookeeper
Or, if you don't want/need a background service you can just run:
  zkServer start
==> Analytics
install: 7,043 (30 days), 21,263 (90 days), 79,252 (365 days)
install_on_request: 2,192 (30 days), 6,831 (90 days), 25,814 (365 days)
build_error: 0 (30 days)
```
brew nstall 完成之后 对应配置如下
```$xslt
/usr/local/etc/zookeeper
chengkejundeMacBook-Pro:zookeeper c.kj$ ls
defaults		log4j.properties	zoo.cfg			zoo_sample.cfg
chengkejundeMacBook-Pro:zookeeper c.kj$ 
```
修改 zoo.cfg 搭建集群修改这些配置 因为只需要搭建一个伪集群所以没有修改其中参数，感兴趣的可以自己搜索一些教程～
```$xslt
# The number of milliseconds of each tick
tickTime=2000
# The number of ticks that the initial 
# synchronization phase can take
initLimit=10
# The number of ticks that can pass between 
# sending a request and getting an acknowledgement
syncLimit=5
# the directory where the snapshot is stored.
# do not use /tmp for storage, /tmp here is just 
# example sakes.
dataDir=/usr/local/Cellar/zookeeper/data
# the port at which the clients will connect
clientPort=2181
# the maximum number of client connections.
# increase this if you need to handle more clients
#maxClientCnxns=60
#
# Be sure to read the maintenance section of the 
# administrator guide before turning on autopurge.
#
# http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance
#
# The number of snapshots to retain in dataDir
#autopurge.snapRetainCount=3
# Purge task interval in hours
# Set to "0" to disable auto purge feature
#autopurge.purgeInterval=1

``` 
为了能够在任意目录启动zookeeper集群，我们需要配置环境变量.
 <br> ps:你也可以不配，这不是搭建集群的必要操作，只不过如果你不配置环境变量，那么每次启动zookeeper需要到安装文件的 bin 目录下去启动。
 <br> 配置如下
```$xslt
chengkejundeMacBook-Pro:data c.kj$ open -e ~/.bash_profile
chengkejundeMacBook-Pro:data c.kj$ cat ~/.bash_profile
export M3_HOME=/usr/local/mvn/apache-maven-3.3.9
export GRADLE_HOME=/usr/local/Cellar/gradle/5.2.1
export SCALA_HOME=/usr/local/Cellar/scala/2.12.8 
export ZK_HOME=/usr/local/Cellar/zookeeper/3.4.13
export PATH=$M3_HOME/bin:$PATH:$GRADLE_HOME/bin:$SCALA_HOME/bin:$ZK_HOME/binchengkejundeMacBook-Pro:data c.kj$ 
chengkejundeMacBook-Pro:data c.kj$ 
chengkejundeMacBook-Pro:data c.kj$ source ~/.bash_profile

```

启动命令：
``
zkServer start
``

停止命令：
``
zkServer stop
``

重启命令：
``
zkServer restart
``

查看集群节点状态：
``
zkServer status
``
```$xslt
chengkejundeMacBook-Pro:~ c.kj$ zkServer start
ZooKeeper JMX enabled by default
Using config: /usr/local/etc/zookeeper/zoo.cfg
Starting zookeeper ... already running as process 49422.
chengkejundeMacBook-Pro:~ c.kj$ zkServer status
ZooKeeper JMX enabled by default
Using config: /usr/local/etc/zookeeper/zoo.cfg
Mode: standalone
chengkejundeMacBook-Pro:~ c.kj$ 
```
end ~
