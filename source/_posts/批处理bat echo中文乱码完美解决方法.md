---
layout:
title: 批处理bat echo中文乱码完美解决方法
date: 2020-09-10 13:37:16
tags: 批处理
---

转自:https://blog.csdn.net/tp7309/article/details/79283524

```
@echo off
REM 声明采用UTF-8编码
chcp 65001
echo test
echo 中文测试
pause
```
