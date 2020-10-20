---
title: Spring中访问MongoDB
date: 2020-10-20 23:19:39
tags:
 - mongodb
 - 分布式
categories:
 - 分布式技术
---

# Spring中访问MongoDB

## 环境准备
### 初始化MongoDB的库及权限

**创建库**
```
use springbucks
```
 
**创建用户**
```
db.createUser(
    {
        user:"springbucks",
        pwd:"springbucks",
        roles:[
            {role:"readWrite",db:"springbucks"}
        ]
    }
)
```
TODO:


