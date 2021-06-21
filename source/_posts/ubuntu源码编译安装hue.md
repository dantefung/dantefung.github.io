---
title: ubuntu源码编译安装hue
date: 2021-06-18 17:12:59
tags:
 - 运维
categories:
 - 运维
---

# ubuntu源码编译安装hue

## 简介

Hue是一个开源的Apache Hadoop UI系统，最早是由Cloudera Desktop演化而来，
由Cloudera贡献给开源社区，它是基于Python Web框架Django实现的。
通过使用Hue我们可以在浏览器端的Web控制台上与Hadoop集群进行交互来分析处理数据，
例如操作HDFS上的数据，运行MapReduce Job,Hive等等。

## 功能介绍

数据库查询编辑器,支持 Hive, Impala, MySql, PostGres, Sqlite and Oracle
动态查询仪表盘,支持 Solr
支持 Spark 编辑器和仪表盘
浏览器查看状态,支持 YARN, HDFS, Hive table Metastore, HBase, ZooKeeper
支持 Pig Editor, Sqoop2, Oozie workflows 编辑器和仪表盘
将数据导入hdfs


## 安装包下载地址

https://github.com/cloudera/hue/releases/tag/release-4.10.0


## 创建用户组及用户

```
groupadd hue
useradd -g hue hue -m -s /bin/bash
passwd hue # 为hue用户设置密码为hue
chown -R hue.hue /opt/hue-release-4.10.0
su - hue
```

## 环境要求

### 前置条件
- maven3.6
- jdk1.8
- mysql8.0
- python2.7
- npm


```

sudo apt-get install maven
sudo apt-get install npm
sudo apt-get install mysql
```

``` 
 sudo apt-get install ant gcc g++ libkrb5-dev libffi-dev libmysqlclient-dev libssl-dev libsasl2-dev libsasl2-modules-gssapi-mit libsqlite3-dev libtidy-0.99-0 libxml2-dev libxslt-dev make libldap2-dev maven python-dev python-setuptools libgmp3-dev
```

## 编译安装

```
cd /opt/hue-release-4.10.0
sudo make apps
```

{% asset_img 2021-06-18-17-26-05.png %}

## 修改配置文件

``` 
vi /home/hue/hue/desktop/conf/pseudo-distributed.ini
```

``` 
[desktop]
       # 安全秘钥，存储session的加密处理
       secret_key=malsjljljwrlwerwel
       # 不启用的模块
       app_blacklist=impala,security,filebrowser,jobbrowser,rdbms,jobsub，pig,hbase,sqoop,zookeeper,metastore,spark,oozie,indexer 
```

```
[[database]]
       # 数据库引擎类型
       engine=mysql
       # 数据库主机地址
       host=192.168.221.130
       # 数据库端口
       port=3306
       # 数据库用户名
       user=hue
       # 数据库密码
       password=hue
       # 数据库库名
       name=hue
```

## 初始化数据库

创建数据库，数据库：hue，用户：hue，密码：hue
```
CREATE DATABASE IF NOT EXISTS hue DEFAULT CHARSET utf8 COLLATE utf8_general_ci;
CREATE USER 'hue'@'%'IDENTIFIED BY 'hue';
GRANT ALL PRIVILEGES ON hue.* TO 'hue'@'%';
FLUSH PRIVILEGES;
```

初始化数据库

```
sudo ./build/env/bin/hue syncdb
sudo ./build/env/bin/hue migrate
```

{% asset_img 2021-06-18-17-49-29.png %}


## 启动系统服务，并通过浏览器访问

```
sudo chown -R gtland.gtland /opt/hue-release-4.10.0
```

启动服务:

```
nohup ./build/env/bin/supervisor >>/dev/null 2>&1 &
```

浏览器访问:

```
http://192.168.221.130:8000/

```

{% asset_img 2021-06-18-18-26-44.png %}

**启动与关闭:**
```
# 启动hue
cd /usr/local/hue/build/env/bin
supervisor &
# 关闭hue
pkill -U hue
或者
killall -u hue
```


## HUE 集成Hadoop

1、**修改core-site.xml**
```
cd /opt/hadoop-2.10.1/etc/hadoop
vi core-site.xml 
```

```
<property>
       <name>hadoop.proxyuser.hue.hosts</name>
       <value>*</value>
</property>
<property>
       <name>hadoop.proxyuser.hue.groups</name>
       <value>*</value>
</property>
```

2、**修改hdfs-site.xml**

```
cd /opt/hadoop-2.10.1/etc/hadoop
vi hdfs-site.xml
```

```
<property>
 <name>dfs.webhdfs.enabled</name>
 <value>true</value>
</property>
```

**修改httpfs-site.xml**

```
  <property>
        <name>httpfs.proxyuser.hadoop.hosts</name>
        <value>*</value>
    </property>
    <property>
        <name>httpfs.proxyuser.hadoop.groups</name>
        <value>*</value>
    </property>
```

**3、重启 HDFS**

**4、修改 pseudo-distributed.ini**
```
 sudo vi pseudo-distributed.ini
```

```
[hadoop]

  # Configuration for HDFS NameNode
  # ------------------------------------------------------------------------
  [[hdfs_clusters]]
    # HA support by using HttpFs

    [[[default]]]
      # Enter the filesystem uri
      fs_defaultfs=hdfs://10.10.27.70:9000

      # NameNode logical name.
      ## logical_name=

      # Use WebHdfs/HttpFs as the communication mechanism.
      # Domain should be the NameNode or HttpFs host.
      # Default port is 14000 for HttpFs.
      ## webhdfs_url=http://localhost:50070/webhdfs/v1
      webhdfs_url=http://10.10.27.70:50070/webhdfs/1  
```


## HUE 集成yarn

**1.修改hue的配置文件( pseudo-distributed.ini)**

{% asset_img 2021-06-21-17-05-41.png %}

**2.开启yarn日志聚合服务**

修改 hadoop的配置文件(yarn-site.xml)
```
<!-- 是否启用日志聚集功能。-->
<property>
<name>yarn.log-aggregation-enable</name>
<value>true</value>
</property>
<!-- 设置日志保留时间，单位是秒 -->
<property>
<name>yarn.log-aggregation.retain-seconds</name>
<value>106800</value>
</property>

```

**3.重启yarn,hue**

重启hadoop集群
```
stop-all-sh
start-all.sh
```

重启hue
```
su hue
cd /opt/hue-release-4.10.0/opt/hue-
build/env/bin/supervisor
```

## HUE集成Hbase

Hbase配置文件-hbase-site.xml
```
<property>
  <name>hbase.thrift.support.proxyuser</name>
  <value>true</value>
</property>
 
<property>
  <name>hbase.regionserver.thrift.http</name>
  <value>true</value>
</property>
```

core-site.xml添加

```

<property>
    <name>hadoop.proxyuser.hue.hosts</name>
    <value>*</value>
</property>
<property>
    <name>hadoop.proxyuser.hue.groups</name>
    <value>*</value>
</property>
<property>
    <name>hadoop.proxyuser.hadoop.hosts</name>
    <value>*</value>
</property>
<property>
    <name>hadoop.proxyuser.hadoop.groups</name>
    <value>*</value>
</property>

```

```
 sudo vi pseudo-distributed.ini
```

```
[hbase]
hbase_conf_dir={{HBASE_CONF_DIR}}
thrift_transport=buffered
```


```
hbase-daemon.sh stop thrift
hbase-daemon.sh start thrift
```


## issues
`Hue _mysql.c:44:10: fatal error: my_config.h: No such file or directory`

``` 
I could not find any package for CentOS 8 with that file. However, I found the source file for 8.0.11 on MySQL’s official site.
 
https://dev.mysql.com/doc/dev/mysql-server/8.0.11/my__config_8h_source.html
 
I created a my_config.h file under /usr/include and copy the content of the source file to the file I just created. I was able to install MySQL-python module afterward.
```

`hue使用hbase报错User: root is not allowed to impersonate admin`

hadoop 的 core-site.xml
```
<property>
            <name>hadoop.proxyuser.xxx.hosts</name>
            <value>*</value>
     </property>
    <property>
            <name>hadoop.proxyuser.xxx.groups</name>
            <value>*</value>
    </property>

```

User: root is not allowed to impersonate admin

User:后面是什么xxx就是什么
