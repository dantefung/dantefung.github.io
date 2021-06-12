---
title: zookeeper集群搭建
date: 2021-06-12 11:22:53
tags:
 - 运维
categories:
 - 运维
---

## zookeeper集群搭建

### 环境要求

- 安装JDK8

### 服务器规划

在局域网内规划三台机器:

|IP|域名|系统版本号|
|---|---|---|
|10.10.x.70|zk.had01|Linux version 5.4.0-70-generic (buildd@lcy01-amd64-004) (gcc version 9.3.0 (Ubuntu 9.3.0-17ubuntu1~20.04)) #78-Ubuntu SMP Fri Mar 19 13:29:52 UTC 2021|
|10.10.x.8|zk.had02|Linux version 5.4.0-70-generic (buildd@lcy01-amd64-004) (gcc version 9.3.0 (Ubuntu 9.3.0-17ubuntu1~20.04)) #78-Ubuntu SMP Fri Mar 19 13:29:52 UTC 2021|
|10.187.x.120|zk.had03|Linux version 3.10.0-957.el7.x86_64 (mockbuild@kbuilder.bsys.centos.org) (gcc version 4.8.5 20150623 (Red Hat 4.8.5-36) (GCC) ) #1 SMP Thu Nov 8 23:39:32 UTC 2018|


**host文件配置**
```
10.10.27.70    zk.had01
10.10.27.8     zk.had02
10.187.100.120 zk.had03
```

### 架构图
{% asset_img 2021-06-12-17-03-53.png %}

### 下载zookeeper并安装

```
cd /opt

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

```
cd data
vi myid
```
> Note:
> 根据我们的配置文件：
> server.1=zk.had01:2888:3888
> server.2=zk.had02:2888:3888
> server.3=zk.had03:2888:3888
> 所以: 
> zk.had01 的myid 为 1
> zk.had02 的myid 为 2
> zk.had03 的myid 为 3


将配置好的整个zookeeper-3.4.10文件夹压缩
```
cd /opt
tar -cvf zookeeper-3.4.10.tar zookeeper.3.4.10
```
即将刚压缩好的文件夹传送到另外的两台服务器, zk.had02, zk.had03 
```
scp zookeeper-3.4.10.tar yourusername@yourip:/tmp
```
分别登陆zk.had02和zk.had03将压缩包zookeeper-3.4.10.tar移至/opt目录下
```
mv /tmp/zookeeper-3.4.10 /opt
```
解压该压缩文件
```
tar -xvf zookeeper-3.4.10.tar
```
修改myid
```
cd /opt/zookeeper-3.4.10/data/
vi myid
```
zk.had02节点的myid修改为2， zk.had03节点的myid修改为3.

### 启动zookeeper集群
分别ssh进入zk.had01, zk.had02, zk.had03 
```
cd /opt/zookeeper-3.4.10/bin
./zkServer.sh start
```
待三台机器都起来后, 再键入
```
./zkServer.sh status
```



**注意事项:** 
防火墙的设置，保证ip和端口都能相通.

## 巨人的肩膀
- [1、zookeeper集群安装](https://www.cnblogs.com/netbloomy/p/6658041.html)


