---
title: Docker中Redis挂载及密码设置
date: 2021-01-20 17:47:50
tags:
  - 互联网工程
  - 运维
categories:
  - 互联网工程
  - 运维
---

# Docker中Redis挂载及密码设置

## 拉取镜像
```
docker pull redis
```
## 启动容器
```
docker run -d --name redis-server -p 6379:6379 -v /Users/admin/Documents/software/docker_volume_mapping/redis/redis_conf:/etc/redis/redis.conf -v /Users/admin/Documents/software/docker_volume_mapping/redis/redis_data:/data redis:latest /etc/redis/redis.conf --appendonly yes --requirepass "redis"
```
or
```
docker run -d --name redis-server -p 6379:6379 -v /usr/redis/redis.conf:/etc/redis/redis.conf -v /usr/redis/data/:/data redis:latest /etc/redis/redis.conf --appendonly yes --requirepass "redis123"
```
> NOTE:
> 在/usr/redis新建文件夹，拷贝redis.conf配置文件，建data文件夹保存redis持久化数据      
> -v 挂在目录，这里本别挂在了redis.conf文件和data文件夹，     
> /etc/redis/redis.conf 关键配置，让redis以指定的配置文件启动，而不是默认无配置启动    
> --appendonly yes redis启动后开启数据持久化
