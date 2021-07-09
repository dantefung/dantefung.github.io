---
title: hive中创建dual虚表
date: 2021-06-25 18:37:34
tags:
 - hive
 - 大数据
categories: 
 - hive
 - 大数据
---

# hive中创建dual虚表


**1、通过如下方式创建dual.txt文件.**
```
cd /tmp
echo 'X' > dual.txt
```

**2、在hive中创建虚表**
```
create table dual(dummy string);
```

**3、将dual.txt文件load到虚表中**
```
load data local inpath '/tmp/dual.txt' overwrite into table dual;
```

**4、测试虚表**
```
select 1 from dual;
select 'hello world' from dual;
```

## Reference
- [hive中构建dual虚表](https://blog.csdn.net/qq_20641565/article/details/52927511)
