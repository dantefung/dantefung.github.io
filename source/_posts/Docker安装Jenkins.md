---
title: Docker安装Jenkins
date: 2021-01-21 10:42:27
tags:
 - 互联网工程
categories:
 - 互联网工程
---

# Docker 安装 Jenkins

## 前置条件

- 服务器安装了docker
- docker启动时，需要开启tcp端口

## Jenkins 安装

### 命令行启动
```
docker run -p 8080:8080 -p 50000:50000 -d  -v /home/jenkins-home-docker:/var/jenkins_home  jenkins/jenkins:lts
```

```
docker run -p 8080:8080 -p 50000:50000 -d  -v D:/software/data/docker/jenkins2.60.3:/var/jenkins_home  jenkins:2.60.3
```

### 配置Jenkins

访问http://<你的ip>:8080访问Jenkins。如果无法访问请检查系统防火墙、云的安全组设置。

{% asset_img 2021-01-21-10-52-33.png %}

根据提示需要输入密码解锁Jenkins。

**查看初始化的密码:**

进入容器:
```
docker exec -it jenkins /bin/bash
```
查看密码:
```
cat /var/jenkins_home/secrets/initialAdminPassword
```
{% asset_img 2021-01-21-10-57-36.png %}

{% asset_img 2021-01-21-10-59-02.png %}

{% asset_img 2021-01-21-10-59-15.png %}

{% asset_img 2021-01-21-10-59-36.png %}

{% asset_img 2021-01-21-11-03-49.png %}

{% asset_img 2021-01-21-11-20-41.png %}

{% asset_img 2021-01-21-11-24-08.png %}

{% asset_img 2021-01-21-13-45-30.png %}

{% asset_img 2021-01-21-13-50-35.png %}

{% asset_img 2021-01-21-13-53-26.png %}

{% asset_img 2021-01-21-13-53-52.png %}


### 配置加速器

{% asset_img 2021-01-21-15-16-20.png %}

### Reference
- [User Handbook overview](https://www.jenkins.io/doc/book/getting-started/)
