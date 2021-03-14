---
title: 通过docker快速搭建rocketmq环境
date: 2021-03-14 23:12:43
tags:
 - 互联网工程
 - Docker
 - RocketMQ
categories:
 - 互联网工程
---

# 通过 docker 快速搭建 rocketmq 环境

## 1. 安装 Namesrv
### 拉取镜像
```
docker pull rocketmqinc/rocketmq:4.4.0`
```
### 启动容器
```
docker run -d -p 9876:9876 -v {RmHome}/data/namesrv/logs:/root/logs -v {RmHome}/data/namesrv/store:/root/store --name rmqnamesrv -e "MAX_POSSIBLE_HEAP=100000000" rocketmqinc/rocketmq:4.4.0 sh mqnamesrv
```
**注意事项:**
 {RmHome} 要替换成你的宿主机想保存 MQ 的日志与数据的地方，通过 docker 的 -v 参数使用 volume 功能，把你本地的目录映射到容器内的目录上。否则所有数据都默认保存在容器运行时的内存中，重启之后就又回到最初的起点。

## 2. 安装 broker 服务器
### 拉取镜像
与上步是同一个镜像，如果上步完成，此步无需拉取
创建 broker.conf 文件
1. 在 {RmHome}/conf 目录下创建 broker.conf 文件
2. 在 broker.conf 中写入如下内容
```
brokerClusterName = DefaultCluster
brokerName = broker-a
brokerId = 0
deleteWhen = 04
fileReservedTime = 48
brokerRole = ASYNC_MASTER
flushDiskType = ASYNC_FLUSH
brokerIP1 = {本地外网 IP}
```
brokerIP1 要修改成你自己宿主机的 IP

### 启动容器
~~~ 
docker run -d -p 10911:10911 -p 10909:10909 -v {RmHome}/data/broker/logs:/root/logs -v {RmHome}/rocketmq/data/broker/store:/root/store -v {RmHome}/conf/broker.conf:/opt/rocketmq-4.4.0/conf/broker.conf --name rmqbroker --link rmqnamesrv:namesrv -e "NAMESRV_ADDR=namesrv:9876" -e "MAX_POSSIBLE_HEAP=200000000" rocketmqinc/rocketmq:4.4.0 sh mqbroker -c /opt/rocketmq-4.4.0/conf/broker.conf
~~~
```
docker run -u root -d -p 9876:9876 -v /Users/admin/Documents/software/docker_volume_mapping/rocketmq/data/namesrv/logs:/root/logs -v /Users/admin/Documents/software/docker_volume_mapping/rocketmq/data/namesrv/store:/root/store -v /Users/admin/Documents/software/docker_volume_mapping/rocketmq/conf/broker.conf:/opt/rocketmq-4.4.0/conf/broker.conf --name rmqnamesrv -e "MAX_POSSIBLE_HEAP=100000000" --privileged=true rocketmqinc/rocketmq:4.4.0 sh mqnamesrv
```
笔者在/User/admin/Documents/software/docker_volume_mapping/rocketmq/data/conf/broker.conf的内容如下:
```
# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
#  Unless required by applicable law or agreed to in writing, software
#  distributed under the License is distributed on an "AS IS" BASIS,
#  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
#  See the License for the specific language governing permissions and
#  limitations under the License.


# 所属集群名字
brokerClusterName=DefaultCluster

# broker 名字，注意此处不同的配置文件填写的不一样，如果在 broker-a.properties 使用: broker-a,
# 在 broker-b.properties 使用: broker-b
brokerName=broker-a

# 0 表示 Master，> 0 表示 Slave
brokerId=0

# nameServer地址，分号分割
# namesrvAddr=rocketmq-nameserver1:9876;rocketmq-nameserver2:9876

# 启动IP,如果 docker 报 com.alibaba.rocketmq.remoting.exception.RemotingConnectException: connect to <192.168.0.120:10909> failed
# 解决方式1 加上一句 producer.setVipChannelEnabled(false);，解决方式2 brokerIP1 设置宿主机IP，不要使用docker 内部IP
# 此处为笔者的外网IP
 brokerIP1=192.168.1.2

# 在发送消息时，自动创建服务器不存在的topic，默认创建的队列数
defaultTopicQueueNums=4

# 是否允许 Broker 自动创建 Topic，建议线下开启，线上关闭 ！！！这里仔细看是 false，false，false
autoCreateTopicEnable=true

# 是否允许 Broker 自动创建订阅组，建议线下开启，线上关闭
autoCreateSubscriptionGroup=true

# Broker 对外服务的监听端口
listenPort=10911

# 删除文件时间点，默认凌晨4点
deleteWhen=04

# 文件保留时间，默认48小时
fileReservedTime=120

# commitLog 每个文件的大小默认1G
mapedFileSizeCommitLog=1073741824

# ConsumeQueue 每个文件默认存 30W 条，根据业务情况调整
mapedFileSizeConsumeQueue=300000

# destroyMapedFileIntervalForcibly=120000
# redeleteHangedFileInterval=120000
# 检测物理文件磁盘空间
diskMaxUsedSpaceRatio=88
# 存储路径
# storePathRootDir=/home/ztztdata/rocketmq-all-4.1.0-incubating/store
# commitLog 存储路径
# storePathCommitLog=/home/ztztdata/rocketmq-all-4.1.0-incubating/store/commitlog
# 消费队列存储
# storePathConsumeQueue=/home/ztztdata/rocketmq-all-4.1.0-incubating/store/consumequeue
# 消息索引存储路径
# storePathIndex=/home/ztztdata/rocketmq-all-4.1.0-incubating/store/index
# checkpoint 文件存储路径
# storeCheckpoint=/home/ztztdata/rocketmq-all-4.1.0-incubating/store/checkpoint
# abort 文件存储路径
# abortFile=/home/ztztdata/rocketmq-all-4.1.0-incubating/store/abort
# 限制的消息大小
maxMessageSize=65536

# flushCommitLogLeastPages=4
# flushConsumeQueueLeastPages=2
# flushCommitLogThoroughInterval=10000
# flushConsumeQueueThoroughInterval=60000

# Broker 的角色
# - ASYNC_MASTER 异步复制Master
# - SYNC_MASTER 同步双写Master
# - SLAVE
brokerRole=ASYNC_MASTER

# 刷盘方式
# - ASYNC_FLUSH 异步刷盘
# - SYNC_FLUSH 同步刷盘
flushDiskType=ASYNC_FLUSH

# 发消息线程池数量
# sendMessageThreadPoolNums=128
# 拉消息线程池数量
# pullMessageThreadPoolNums=128

```

### 启动broker服务

```
docker run -u root -d -p 10911:10911 -p 10909:10909 -v /Users/admin/Documents/software/docker_volume_mapping/rocketmq/data/broker/logs:/root/logs -v /Users/admin/Documents/software/docker_volume_mapping/rocketmq/data/broker/store:/root/store -v /Users/admin/Documents/software/docker_volume_mapping/rocketmq/conf/broker.conf:/opt/rocketmq-4.4.0/conf/broker.conf --name rmqbroker --privileged=true --link rmqnamesrv:namesrv -e "NAMESRV_ADDR=namesrv:9876" -e "MAX_POSSIBLE_HEAP=200000000" rocketmqinc/rocketmq:4.4.0 sh mqbroker -c /opt/rocketmq-4.4.0/conf/broker.conf
```

### 注意事项
注意: {RmHome} 同上步一样，不再缀述。broker.conf 的文件中的 brokerIP1 是你的 broker 注册到 Namesrv 中的 ip。如果不指定他会默认取容器中的内网 IP。除非你的应用也同时部署在网络相通的容器中，本地或容器外就无法连接 broker 服务了，进而导致类似 RemotingTooMuchRequestException 等各种异常。

## 3. 安装 rocketmq 控制台
## 拉取镜像
``` 
docker pull pangliang/rocketmq-console-ng
```
## 启动容器
``` 
docker run -e "JAVA_OPTS=-Drocketmq.namesrv.addr={本地外网 IP}:9876 -Dcom.rocketmq.sendMessageWithVIPChannel=false" -p 8080:8080 -t pangliang/rocketmq-console-ng
```
笔者的命令:
```
docker run -e "JAVA_OPTS=-Drocketmq.namesrv.addr=192.168.1.2:9876 -Dcom.rocketmq.sendMessageWithVIPChannel=false" -p 8080:8080 -t pangliang/rocketmq-console-ng
```
~~~
docker run -e "JAVA_OPTS=-Drocketmq.namesrv.addr=127.0.0.1:9876 -Dcom.rocketmq.sendMessageWithVIPChannel=false" -p 8080:8080 -t styletang/rocketmq-console-ng
~~~

~~~
注意：这里因为我们用了docker，所以namesrv.addr需要换成docker容器的ip
~~~
~~~
查看容器中rmqnamesrv的ip
~~~
```
docker exec -it rmqnamesrv bash
cat /etc/hosts
```
~~~
替换 namesrv.addr 后，启动控制台，访问 {你的IP}:8080
~~~

**笔者自己的Docker启动Broker命令备份**:
``` 
docker run -d -p 10911:10911 -p 10909:10909 -v /Users/admin/Documents/software/docker_volume_mapping/rocketmq/data/broker/logs:/root/logs -v /Users/admin/Documents/software/docker_volume_mapping/rocketmq/data/broker/store:/root/store -v /Users/admin/Documents/software/docker_volume_mapping/rocketmq/conf/broker.conf:/opt/rocketmq-4.4.0/conf/broker.conf --name rmqbroker --link rmqnamesrv:namesrv -e "NAMESRV_ADDR=namesrv:9876" -e "MAX_POSSIBLE_HEAP=200000000" rocketmqinc/rocketmq:4.4.0 sh mqbroker -c /opt/rocketmq-4.4.0/conf/broker.conf
```


```
docker exec --user root -it  a5fe17d55f1c3261b57b7689884d6ae51fd64b88b4cd3fc23c84caaffa19feab /bin/bash
```
