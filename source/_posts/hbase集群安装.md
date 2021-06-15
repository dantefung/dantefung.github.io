---
title: hbase集群安装
date: 2021-06-14 22:31:38
tags:
 - 运维
categories:
 - 运维
---

## Hbase集群安装

### 服务器规划

|IP|域名|系统版本号|
|---|---|---|
|10.10.x.70|zk.had01|Linux version 5.4.0-70-generic (buildd@lcy01-amd64-004) (gcc version 9.3.0 (Ubuntu 9.3.0-17ubuntu1~20.04)) #78-Ubuntu SMP Fri Mar 19 13:29:52 UTC 2021|
|10.10.x.8|zk.had02|Linux version 5.4.0-70-generic (buildd@lcy01-amd64-004) (gcc version 9.3.0 (Ubuntu 9.3.0-17ubuntu1~20.04)) #78-Ubuntu SMP Fri Mar 19 13:29:52 UTC 2021|
|10.187.x.120|zk.had03|Linux version 3.10.0-957.el7.x86_64 (mockbuild@kbuilder.bsys.centos.org) (gcc version 4.8.5 20150623 (Red Hat 4.8.5-36) (GCC) ) #1 SMP Thu Nov 8 23:39:32 UTC 2018

### 环境要求

- JDK8


### host文件

将本地回环地址改成内网地址(hbase需要), 将规划的域名zk.had01, zk.had02, zk.had03的配置如下:

10.10.27.70机器的host配置:
```
root@GT70:~# cat /etc/hosts
#127.0.0.1      localhost
10.10.27.70     GT70
127.0.0.1      localhost
# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
10.10.27.70 zk.had01
10.10.27.8 zk.had02
10.187.100.120 zk.had03
```

10.10.27.8机器的host配置:
```
hadoop@root-RiskCtrl:/opt/hbase234/logs$ cat /etc/hosts
10.10.27.8      root-RiskCtrl
127.0.0.1       localhost

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
10.10.27.70 zk.had01 GT70 master
10.10.27.8 zk.had02 root-RiskCtrl
10.187.100.120 zk.had03 selenium-test
```

10.187.100.120机器的host配置:
```
[hadoop@selenium-test hadoop]$ cat /etc/hosts
10.187.100.120 selenium-test
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.5.28.240    riskmg-dev.gtland.cn
10.5.28.240    authcctr-dev.gtland.cn
10.10.27.70    zk.had01
10.10.27.8     zk.had02   root-RiskCtrl
10.187.100.120 zk.had03
10.10.27.70    GT70 master
```

### 用户及用户组

**用户组、用户、权限规划**

todo ...

### 免密登录

{% asset_img 2021-06-15-23-55-30.png %}

**方式一:**
```
ssh-keygen
ssh-copy-id -p 22612 hadoop@zk.had01
ssh-copy-id -p 22612 hadoop@zk.had02
ssh-copy-id -p 22612 hadoop@zk.had03
```

**方式二:**

将本机的公钥文件上传到目标服务器上，然后在服务器需要免密登录的用户家目录下查看是否有 ~/.ssh/authorized_keys这个文件，
如果没有手动创建一个:

```
touch ~/.ssh/authorized_keys
```

然后我们将公钥内容写入到authorized_keys文件中，因为这个文件可能已经有内容了，所以你可以使用如下方式
```
cat -n ~/.ssh/rsa.pub ~/.ssh/authorized_keys
```
这样就将公钥内容追加到authorized_keys中了，然后需要注意配置权限了，否则SSH不会工作的.

将.ssh目录的权限为700
将authorized_keys目录的权限为600

**设置别名:**

```
vi ~/.ssh/config
```
内容如下:
```
host hadoop1
     user hadoop
     hostname 10.10.27.70
     port 22612

host hadoop2
     user hadoop
     hostname 10.10.27.8
     port 22612

host hadoop3
     user hadoop
     hostname 10.187.100.120
     port 22612
```



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
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
      <!--指定zk的地址,多个用,分割 -->
          <name>hbase.zookeeper.quorum</name>
          <value>zk.had01:2181,zk.had02:2181,zk.had03:2181</value>
  </property>
  <property>
            <!-- 指定 hbase 在 HDFS 上存储的路径 -->
          <name>hbase.rootdir</name>
          <value>hdfs://zk.had01:9000/hbase234</value>
  </property>
  <property>
        <!--zookooper配置、日志等的存储位置，必须为以存在 -->
        <name>hbase.zookeeper.property.dataDir</name>
        <value>/data/hbase/zookeeper</value>
  </property>
  <property>
        <!--hbase web 端口 -->
        <name>hbase.master.info.port</name>
        <value>16610</value>
  </property>

</configuration>

```


**修改regionservers:**

```
hadoop2
hadoop3
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
scp -r hbase-2.3.4/ hadoop@zk.had02:/tmp
scp -r hbase-2.3.4/ hadoop@zk.had03:/tmp
```
分别登陆zk.had02, zk.had03，移动hbase-2.3.4至/opt下
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

```
cd /opt/hbase-2.3.4/bin
./start-hbase.sh
```


## 巨人的肩膀
- [Hadoop集群、Hbase集群指定ssh端口](https://blog.csdn.net/yangrui1985123/article/details/103381243)
- [HBase学习之路 （二）HBase集群安装](https://www.cnblogs.com/qingyunzong/p/8668880.html)
- [Hadoop中ssh+IP、ssh+别名免秘钥登录配置](https://www.cnblogs.com/ysocean/p/6959776.html)