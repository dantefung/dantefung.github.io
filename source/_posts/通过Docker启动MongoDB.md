---
title: 通过Docker启动MongoDB
date: 2020-10-20 23:09:37
tags:
 - mongodb
 - 分布式
 - docker
categories:
 - 分布式技术
---

# 通过Docker启动MongoDB

### 官方指引
- https://hub.docker.com/_/mongo

### 获取镜像
- docker pull mongo

### 运行MongoDB镜像
```
docker run --name mongo -p 27017:27017 -v ~/Documents/software/docker_volume_mapping/mongo:/data/db -e MONGO_INITDB_ROOT_PASSWORD=admin -d mongo
```

```
docker run mongo
```
