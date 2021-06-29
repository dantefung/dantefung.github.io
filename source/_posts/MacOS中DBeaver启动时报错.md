---
title: MacOS中DBeaver启动时报错
date: 2021-06-30 00:50:31
tags:
 - mac
categories:
 - mac 
---

## 解决MacOS DBeaver启动时报错：fail to create java virtual machine

```
vi /Applications/DBeaverEE.app/Contents/Eclipse/dbeaver.ini
```
查看reame.txt：
> -vm <java vm path>
    Use Java VM installed in <java vm path> folder instead of default
    location.

添加如下内容:
```
-vm
/Library/Java/JavaVirtualMachines/jdk1.8.0_221.jdk/Contents/Home/bin/java
```
