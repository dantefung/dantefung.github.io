---
title: Mac使用Privoxy转发Socks5代理
date: 2022-08-21 00:58:29
tags:
    - 代理
---

# Mac使用Privoxy转发Socks5代理

> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.jianshu.com](https://www.jianshu.com/p/f8070c43d502)

### 1. 安装

```
> brew install privoxy
```

### 2. 修改配置文件

##### /usr/local/etc/privoxy/config

forward-socks5 / 127.0.0.1:1086 . # 转发本机端口  
listen-address 0.0.0.0:8118 # 监听 8118 端口

### 3. 运行

```
> brew services start privoxy
```

### 4. 检查状态

```
> ps -ef | grep privoxy
501  9099     1   0  7:56PM ??         0:00.41 /usr/local/Cellar/privoxy/3.0.32/sbin/privoxy --no-daemon /usr/local/etc/privoxy/config
> netstat -an | grep 8118
tcp4       0      0  *.8118                 *.*                    LISTEN
```

### 5. 设置环境变量, IP 为 Privoxy 服务器地址

```
export http_proxy="xxx.xxx.x.x:8118"
export https_proxy="xxx.xxx.x.x:8118"
```