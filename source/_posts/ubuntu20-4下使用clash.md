---
title: ubuntu20.4下使用clash
date: 2021-08-05 01:27:27
tags:
 - ubuntu
 - 科学上网
categories:
 - ubuntu
 - 科学上网
---

# ubuntu20.4下使用clash

## 下载clash

`github 地址`：https://github.com/Dreamacro/clash

`下载最新版本 clash`：https://github.com/Dreamacro/clash/releases

或者通过curl下载:

```
culr -o clash.gz https://github.com/Dreamacro/clash/releases/download/v1.6.5/clash-linux-amd64-v1.6.5.gz

```
解压:
```
tar zxvf  clash-linux-amd64-v1.6.5.gz
```

授权可执行权限:

```
chmod +x clash
```


初始化clash:

```
./clash
```



> 初始化执行 clash 会默认在 ~/.config/clash/ 目录下生成配置文件和全球IP地址库：config.yaml 和 Country.mmdb


## 配置clash

clash 使用 yaml 作为配置文件，配置文件示例可以参考：https://github.com/Dreamacro/clash/wiki/configuration

默认的 clash 配置只有一个端口号，一般服务商会提供完整的配置文件（或者 URL），如果 URL 直接是配置内容，可以直接将配置文件内容复制到文件中

我的服务商提供的是无法直接应用的内容，因此还是从 windows 上的 clash 将配置内容导出来然后再复制到配置的。

再次执行 clash，可以发现执行成功（可以作为守护进程一直执行）

```
./clash
```



> 笔者就是通过windows平台的clash配置文件导出，然后覆盖linux平台下~/.config/clash/config.yml文件.

# Reference
- [ubuntu 20.04 配置使用 clash for linux](http://www.ptbird.cn/ubuntu-2004-clash-for-linux.html)
