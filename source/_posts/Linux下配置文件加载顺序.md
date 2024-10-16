---
title: Linux下配置文件加载顺序
date: 2024-07-06 14:05:49
tags:
    - Linux
---

## Linux下配置文件加载顺序

## 检查的环境变量文件的情况之系统运行 Shell 的方式

> 在登录 Linux 系统并启动一个 bash shell 时，默认情况下 bash 会在若干个文件中查找环境变量的设置，这些文件可统称为系统环境文件，bash 检查的环境变量文件的情况取决于系统运行 Shell 的方式，系统运行 Shell 的方式有 3 种。


**1、登录 shell 方式进入 Linux**

账号和密码——>/etc/profile（全局文件 G1）——>/etc/profile.d/（全局脚本目录 F1）——> ~/.bash_profile（用户文件 U1）——> ~/.bashrc（用户文件 U2）——>/etc/bashrc（全局文件 G2）

**2、非登录 Shell 方式进入 Linux**

不需要输入密码的登录及远程 SSH 连接——>  ~/.bashrc（用户文件 U2）——>/etc/bashrc（全局文件 G2）

## 环境变量初始化与对应文件的加载顺序

1、/etc/profile：系统配置文件，用户登录时读取一次
2、/etc/bash.bashrc：（Ubuntu）系统配置文件，用户登录时读取一次，每次打开一个新终端会话时读取一次。
/etc/bashrc： （Centos）系统配置文件，用户登录时读取一次，每次打开一个新终端会话时读取一次。
3、~/.profile（~/.bash_profile、~/.bash_login）：用户配置文件，用户登录时读取一次
4、~/.bashrc：用户配置文件，用户登录时读取一次，每次打开一个新终端会话时读取一次

对于 ~/.bash_profile、~/.bash_login、~/.profile，如果终端绑定的是 bash，则按照我书写的顺序进行读取（如果存在，就不继续读取）

1、系统配置文件作用于全局，而用户配置文件仅针对当前登录的用户
2、先读取系统配置文件，再读取用户配置文件，用户配置文件的变量和表达式等都继承自系统配置文件

### Reference

- [Linux 配置文件的生效顺序：profile、bashrc 等](https://www.cnblogs.com/andy9468/p/13321905.html)