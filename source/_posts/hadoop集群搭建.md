---
title: hadoop集群搭建
date: 2021-06-12 19:29:07
tags:
 - 运维
categories:
 - 运维
---

## hadoop集群搭建

### 服务器规划

|IP|域名|系统版本号|
|---|---|---|
|10.10.x.70|zk.had01|Linux version 5.4.0-70-generic (buildd@lcy01-amd64-004) (gcc version 9.3.0 (Ubuntu 9.3.0-17ubuntu1~20.04)) #78-Ubuntu SMP Fri Mar 19 13:29:52 UTC 2021|
|10.10.x.8|zk.had02|Linux version 5.4.0-70-generic (buildd@lcy01-amd64-004) (gcc version 9.3.0 (Ubuntu 9.3.0-17ubuntu1~20.04)) #78-Ubuntu SMP Fri Mar 19 13:29:52 UTC 2021|
|10.187.x.120|zk.had03|Linux version 3.10.0-957.el7.x86_64 (mockbuild@kbuilder.bsys.centos.org) (gcc version 4.8.5 20150623 (Red Hat 4.8.5-36) (GCC) ) #1 SMP Thu Nov 8 23:39:32 UTC 2018|


### 环境要求

- JDK8

### 下载软件并安装

[官网下载地址](https://hadoop.apache.org/releases.html)

> Note:
> 本文使用的版本号为hadoop-2.10.1.tar.gz
> 存放地址为/opt

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

| 用户组 | 用户   | 权限                                  |
| ------ | ------ | ------------------------------------- |
| hadoop | hadoop | /opt/hadoop-2.10-1<br />/opt/hbase234 |


新建用户组hadoop
```
groupadd hadoop
```
新建hadoop用户
```
useradd hadoop -g hadoop -m -s /bin/bash 
```
 给已创建的用户hadoop设置密码为hadoop
```
passwd hadoop
```
修改目录所属用户
```
chown -R hadoop.hadoop /opt/hadoop-2.10.1
chown -R hadoop.hadoop /opt/hbase234
```
切换用户hadoop
```
su - hadoop
```

> Note:
> zk.had01, zk.had02, zk.had03 三台机上面都要创建.并切换成hadoop用户.


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


### 修改配置文件

- hadoop-env.sh
- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- slaves

**修改hadoop-env.sh**
改其中的JAVA_HOME为我们安装jdk的路径

```
JAVA_HOME=/usr/local/jdk
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
  <value>hdfs://zk.had01:9000</value>
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
                <value>zk.had01</value>
        </property>
        <property>
                <name>yarn.nodemanager.aux-services</name>
                <value>mapreduce_shuffle</value>                                                        
        </property>

</configuration>
```
**修改slaves文件:**
这里配置的是ssh的别名
```
hadoop2
hadoop3
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
> Note:
> zk.had01, zk.had02, zk.had03 也要配置.



### 分发

将zk.had01的/opt/hadoop-2.10.1分发到zk.had02, zk.had03.
```

scp -r /opt/hadoop-2.10.1 hadoop@zk.had02:/tmp
scp -r /opt/hadoop-2.10.1 hadoop@zk.had03:/tmp
```

分别登陆zk.had02, zk.had03，移动hadoop-2.10.1至/opt下

```
mv /tmp/hbase-2.3.4 /opt
```



### 启动hadoop集群

**格式化一个新的分布式文件系统：**

```
cd /opt/hadoop-2.10.1/bin
hadoop namenode -format
```



**启动：**

在zk.had01上启动:
```
cd /opt/hadoop-2.10.1/sbin
./start-all.sh
```

## 巨人的肩膀

- [Hadoop中ssh+IP、ssh+别名免秘钥登录配置](https://www.cnblogs.com/ysocean/p/6959776.html)

- [Hadoop集群搭建](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/ClusterSetup.html)



