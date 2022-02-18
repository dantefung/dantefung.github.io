---
title: ubuntu安装NVM
date: 2022-2-19 03:28:41
tags:
    - 运维
    - nvm
categories:
    - 运维
---

# ubuntu安装NVM

> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.jianshu.com](https://www.jianshu.com/p/8c3e782c7be1)

### 安装

下载安装脚本，可以使用 cURL 或 Wget，最新版本可以到 github 查看 [https://github.com/nvm-sh/nvm](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fnvm-sh%2Fnvm)

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash
```

在安装的时候 或许会出现错误:# [Failed to connect to raw.githubusercontent.com port 443](https://links.jianshu.com/go?to=https%3A%2F%2Fsegmentfault.com%2Fa%2F1190000021637275)

解决方案:

### 原因

由于某些`你懂的因素`，导致 GitHub 的`raw.githubusercontent.com`域名解析被污染了。

### 解决方法

通过修改`hosts`解决此问题。

**查询真实 IP**

在 [https://www.ipaddress.com/](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.ipaddress.com%2F) 查询 raw.githubusercontent.com 的真实 IP。

**修改 hosts**

```
sudo vim /etc/hosts
```

添加如下内容：

```
199.232.96.133 raw.githubusercontent.com
```

</article>

提示: vim 中 :wq! 为强制保存退出

查看是否已经安装好

```
nvm --version
```

### 使用

查看可供安装的版本

```
nvm ls-remote
```

安装一个 nodejs 版本

```
nvm install v12.16.1
```

查看本地安装的版本

```
nvm ls
```