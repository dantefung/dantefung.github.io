---
title: zookeeper集群搭建
date: 2021-06-12 11:22:53
tags:
 - 运维
categories:
 - 运维
---

## zookeeper集群搭建

### 服务器规划


**host文件配置**
```
10.10.27.70    zk.had01
10.10.27.8     zk.had02
10.187.100.120 zk.had03
```

### 下载zookeeper

```
wget https://archive.apache.org/dist/zookeeper/zookeeper-3.4.10/zookeeper-3.4.10.tar.gz

tar -zxvf zookeeper-3.4.10.tar.gz

chmod +wxr zookeeper-3.4.10

cd zookeeper-3.4.10

mkdir data

mkdir logs

vi conf/zoo.cfg

```

**zoo.cfg内容**
```
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
dataDir=/opt/zookeeper-3.4.10/data
dataLogDir=/opt/zookeeper-3.4.10/logs
server.1=zk.had01:2888:3888
server.2=zk.had02:2888:3888
server.3=zk.had03:2888:3888
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





