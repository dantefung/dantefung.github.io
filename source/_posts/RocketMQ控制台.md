---
title: RocketMQ控制台
date: 2021-03-14 23:25:57
tags:
 - 互联网工程
 - RocketMQ
categories:
 - 互联网工程
---

# RocketMQ控制台

文档地址: RocketMQ-Console-Ng

**拉取源码：**

```
cd /opt
git clone https://github.com/apache/rocketmq-externals.git
```

**修改配置:**

```
cd rocketmq-console/src/main/resources
vi application.properties
```

`配置文件内容:`
将项目使用的rocketmq.config.namesrvAddr配置上去,设置rocketmq.config.isVIPChannel=false
```
server.contextPath=
server.port=8080

### SSL setting
#server.ssl.key-store=classpath:rmqcngkeystore.jks
#server.ssl.key-store-password=rocketmq
#server.ssl.keyStoreType=PKCS12
#server.ssl.keyAlias=rmqcngkey

#spring.application.index=true
spring.application.name=rocketmq-console
spring.http.encoding.charset=UTF-8
spring.http.encoding.enabled=true
spring.http.encoding.force=true
logging.config=classpath:logback.xml
#if this value is empty,use env value rocketmq.config.namesrvAddr  NAMESRV_ADDR | now, you can set it in ops page.default localhost:9876
rocketmq.config.namesrvAddr=
#if you use rocketmq version < 3.5.8, rocketmq.config.isVIPChannel should be false.default true
rocketmq.config.isVIPChannel=
#rocketmq-console's data path:dashboard/monitor
rocketmq.config.dataPath=/tmp/rocketmq-console/data
#set it false if you don't want use dashboard.default true
rocketmq.config.enableDashBoardCollect=true
#set the message track trace topic if you don't want use the default one
rocketmq.config.msgTrackTopicName=
rocketmq.config.ticketKey=ticket

#Must create userInfo file: ${rocketmq.config.dataPath}/users.properties if the login is required
rocketmq.config.loginRequired=false
```
**将rocktmq-console打成jar包，得到rocketmq-console-ng-1.0.0.jar:**
```
mvn clean package -Dmaven.test.skip=true
```
**运行jar包，启动项目，这里也可以设置rocketmq.config.namesrvAdd**
```
java -jar rocketmq-console-ng-1.0.0.jar --server.port=12581 --rocketmq.config.namesrvAddr=182.254.140.224:9876
```


