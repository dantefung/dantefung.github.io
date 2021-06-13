---
title: hadoop集群搭建
date: 2021-06-12 19:29:07
tags:
 - 运维
categories:
 - 运维
---

## hadoop集群搭建

### 环境要求

- JDK8

### 下载软件并安装

[官网下载地址](https://hadoop.apache.org/releases.html)

> Note:
> 本文使用的版本号为hadoop-2.10.1.tar.gz
> 存放地址为/opt


### 修改配置文件

- hadoop-env.sh
- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml

**修改hadoop-env.sh**
改其中的JAVA_HOME为我们安装jdk的路径JAVA_HOME=
```
/usr/local/jd
```
**修改core-site.xml文件**
/opt/hadoop-2.10.1/etc/hadoop/core-site.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
-->

<!-- Put site-specific property overrides in this file. -->
<configuration>
<property>
  <name>fs.defaultFS</name>
  <value>hdfs://hadoop1:9000</value>
 </property>
 <!-- 指定hadoop临时目录 -->
 <property>
  <name>hadoop.tmp.dir</name>
  <value>/opt/hadoop-2.10.1/tmp</value>
 </property>
 <!-- 指定zookeeper地址 -->
 <property>
  <name>ha.zookeeper.quorum</name>
  <value>zk.had01:2181,zk.had02:2181,zk.had03:2181</value>
 </property>
 <property>
  <name>ha.zookeeper.session-timeout.ms</name>
  <value>3000</value>
 </property>
 </configuration>
```
**修改hdfs-site.xml**
/opt/hadoop-2.10.1/etc/hadoop/hdfs-site.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
-->

<!-- Put site-specific property overrides in this file. -->

<configuration>
 <!-- configuration for NameNode:-->
        <property>
                <name>dfs.namenode.name.dir</name>
                <value>/opt/hadoop-2.10.1/name</value>
        </property>
        <!-- 块大小 -->
        <property>
                <name>dfs.blocksize</name>
                <value>268435456</value>
        </property>
        <property>
                <name>dfs.namenode.handler.count</name>
                <value>100</value>
        </property>
                <!-- configuration for DateNode:-->
        <property>
                <name>dfs.datanode.data.dir</name>
                <value>/opt/hadoop-2.10.1/data</value>
        </property>
        <property>
                <name>dfs.replication</name>
                <value>1</value>
        </property>
</configuration>
```
**修改mapred-site.xml**
```
<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
-->

<!-- Put site-specific property overrides in this file. -->

<configuration>
        <property>
            <name>mapreduce.framework.name</name>
            <value>yarn</value>
        </property>
</configuration>
```

**修改yarn-site.xml**
```
<?xml version="1.0"?>
<!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
-->
<configuration>

<!-- Site specific YARN configuration properties -->
        <property>
                <name>yarn.resourcemanager.hostname</name>
                <value>hadoop1</value>
        </property>
        <property>
                <name>yarn.nodemanager.aux-services</name>
                <value>mapreduce_shuffle</value>                                                        
        </property>

</configuration>
```
### 配置hadoop环境变量

方便执行hadoop相关的命令.
```
vi /etc/profile
```

内容如下:
```
JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
HADOOP_HOME=/opt/hadoop-2.10.1
PATH=$JAVA_HOME/bin:$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin

export JAVA_HOME
export HADOOP_HOME
export PATH
```

### 无密登陆配置






