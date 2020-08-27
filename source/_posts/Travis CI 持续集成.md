---
title: Travis CI 持续集成
author: dantefung
tags:

  - 

categories:

  - devops

date: 2020年02月25日 16:04:09
---

# Travis CI 持续集成

[TOC]

### 持续集成

>  **持续集成(Continuous Integration):** 是一种软件开发实践。在持续集成中，团队成员频繁集成他们的工作成果，一般每人每天至少集成一次，也可以多次。每次集成会经过自动构建(包括自动测试)的检验，以尽快发现集成错误。

### Travis CI

> Travis CI 是目前新兴开源持续集成构建项目，它与jenkins，GO的明显差别再约采用yaml格式，同时它是在线的服务，不想jenkins需要你在本地搭建服务，简介清新独树一帜。目前大多数的Github项目都已经移入到Travis CI的构建队列中，据说Travis CI每天运行超过4000次完整的构建。对于做开源项目或者Github的使用者，如果你项目还没有加入Travis CI构建队列，那你真的是out of date了。

### User Guide

First time here ? Let 's get you started !

1. Activate your GitHub repositories
   
   激活你的Github仓库。

2. Add a .travis.yml file to your repository
   
   在你的仓库添加`.travis.yml` [^1] 配置文件。

3. Trigger your first build

[^1]


