---
title: hbase集群安装
date: 2021-06-14 22:31:38
tags:
 - 运维
categories:
 - 运维
---

## Hbase集群安装

### 下载Hbase
```
cd /opt
wget http://mirrors.hust.edu.cn/apache/hbase/stable/hbase-2.3.4-bin.tar.gz
```

### 解压
```
tar -zxvf hbase-2.3.4-bin.tar.gz
```

### 修改配置文件

**修改hbase-env.sh:**
```
cd hbase-2.3.4/conf
vi hbase-env.sh
```
```
export JAVA_HOME=/usr/local/jdk1.8.0_73
export HBASE_MANAGES_ZK=false
```

**修改hbase-site.xml:**

```
<configuration>

        <property>
                <!-- 指定 hbase 在 HDFS 上存储的路径 -->
                <name>hbase.rootdir</name>
                <value>hdfs://myha01/hbase126</value>
        </property>
        <property>
                <!-- 指定 hbase 是分布式的 -->
                <name>hbase.cluster.distributed</name>
                <value>true</value>
        </property>
        <property>
                <!-- 指定 zk 的地址，多个用“,”分割 -->
                <name>hbase.zookeeper.quorum</name>
                <value>zk.had01:2181,zk.had02:2181,zk.had03:2181</value>
        </property>

</configuration>

```


**修改regionservers:**

```
zk.had01
zk.had02
zk.had03
```

**修改backup-masters:**

```
zk.had02
```

**修改hdfs-site.xml 和 core-site.xml:**
最重要一步，要把 hadoop 的 hdfs-site.xml 和 core-site.xml 放到 hbase-2.3.4/conf 下
```
cd /opt/hadoop-2.10.1/etc/hadoop
cp core-site.xml hdfs-site.xml /opt/hbase-2.3.4/conf/
```

### 将Hbase安装包分发到其他节点

分发之前先删除HBase目录下的docs文件夹
```
cd /opt/hbase-2.3.4/
rm -rf docs
```
分发
```
scp -r hbase-2.3.4/ riskctrl@zk.had02:/tmp
scp -r hbase-2.3.4/ yourusername@yourdomain:/tmp
scp -r hbase-2.3.4/ yourusername@yourdomain:/tmp
```
分别登陆zk.had01, zk.had02, zk.had03，移动hbase-2.3.4至/opt下
```
mv /tmp/hbase-2.3.4 /opt
```

### 同步时间
HBase 集群对于时间的同步要求的比 HDFS 严格，所以，集群启动之前千万记住要进行 时间同步，要求相差不要超过 30s

### 配置环境变量
```
vi ~/.bashrc 
```
内容如下:
```
#HBase
export HBASE_HOME=/opt/hbase-2.3.4/
export PATH=$PATH:$HBASE_HOME/bin
```
使环境变量立即生效
```
source ~/.bashrc
```
## 启动Hbase集群

### 免密登录
```
ssh-keygen
ssh-copy-id root@zk.had01
ssh-copy-id root@zk.had02
ssh-copy-id -p 22612 root@zk.had03
```


### 启动zookeeper集群
每个zookeeper节点都要执行以下命令
```
zkServer.sh start
```

### 启动HDFS集群及YARN集群
如果需要运行MapReduce程序则启动yarn集群，否则不需要启动
```
start-dfs.sh
```
启动完成之后检查以下namenode的状态
```
hdfs haadmin -getServiceState nn1
hdfs haadmin -getServiceState nn2
```

### 启动HBase



## 巨人的肩膀
- [Hadoop集群、Hbase集群指定ssh端口](https://blog.csdn.net/yangrui1985123/article/details/103381243)
- [HBase学习之路 （二）HBase集群安装](https://www.cnblogs.com/qingyunzong/p/8668880.html)