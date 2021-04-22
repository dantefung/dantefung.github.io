---
title: MySQL基准测试
date: 2021-04-22 22:31:42
tags:
  - MySQL
---

# MySQL基准测试

测量系统性能衡量调优方案是否有效。

## 一、什么是基准测试?

**定义:**
    基准测试是一种测量和评估软件性能指标的活动，用于简历某个时刻的性能基准，以便当系统发生软硬件变化时重新进行基准测试以评估变化对性能的影响。

{% asset_img 2021-04-22-22-35-54.png %}

{% asset_img 2021-04-22-22-36-39.png %}

{% asset_img 2021-04-22-22-37-26.png %}

## 二、如何进行基准测试?

**基准测试的目的:**
- 建立MySQL服务器的性能基准线
  确定当前MySQL服务器的运行情况
- 模拟比当前系统更高的负载，以找出系统的扩展瓶颈
  增加数据库并发，观察QPS, TPS变化， 确定并发量与性能最优的关系。
- 测试不同的硬件、软件和操作系统配置
- 证明新的硬件设备是否配置正确

### 1、对整个系统进行基准测试
从系统入口进行测试(如网站Web前端，手机App前端)

`优点`: 

- 能够测试整个系统的性能，包括web服务器缓存、数据库等。
  
- 能够反映出系统中各个组件接口间的性能问题体现真实性能状况。


`缺点`:

- 测试设计复杂，消耗时间长。

### 2、单独对MySQL进行基准测试
`优点`:
 - 测试设计简单，所需消费时间短
`缺点`:
- 无法全面了解整个系统的性能基线

## 三、MySQL基准测试的常见指标

- 单位时间内所处理的事务数(TPS)

- 单位时间内所处理的查询数(QPS)
  
- 响应时间

  平均相应时间、最小相应时间、最大相应时间、各时间所占百分比

- 并发量： 同时处理的查询请求的数量
  
> Note：并发量不等于连接数

  正在工作中的并发的操作数或同时工作的数量。

## 四、基准测试的步骤

### 计划和设计基准测试

- 对整个系统还是对某一组件

- 使用什么样的数据
  
生产环境的备份数据或日志数据进行回放

- 准备基准测试及数据收集脚本
Get_Test_info.sh
```
#!/bin/bash
INTERVAL=5
PREFIX=/home/imooc/benchmarks/$INTERVAL-sec-status
RUNFILE=/home/imooc/benchmarks/running
echo "1" > $RUNFILE
MYSQL=/usr/local/mysql/bin/mysql
$MYSQL -e "show global variables" >> mysql-variables
while test -e $RUNFILE; do
	file=$(date +%F_%I)
	sleep=$(date +%s.%N | awk '{print 5 - ($1 % 5)}')
	sleep $sleep
	ts="$(date +"TS %s.%N %F %T")"
	loadavg="$(uptime)"
	echo "$ts $loadavg" >> $PREFIX-${file}-status
	$MYSQL -e "show global status" >> $PREFIX-${file}-status &
	echo "$ts $loadavg" >> $PREFIX-${file}-innodbstatus
	$MYSQL -e "show engine innodb status" >> $PREFIX-${file}-innodbstatus &
	echo "$ts $loadavg" >> $PREFIX-${file}-processlist
	$MYSQL -e "show full processlist\G" >> $PREFIX-${file}-processlist &
	echo $ts
done
echo Exiting because $RUNFILE does not exists
```
- 运行基准测试
- 保存及分析基准测试结果
analyze.sh
```
#!/bin/bash
awk '
   BEGIN {
     printf "#ts date time load QPS";
     fmt=" %.2f";
   }
   /^TS/ {
   ts = substr($2,1,index($2,".")-1);
   load = NF -2;
   diff = ts - prev_ts;
   printf "\n%s %s %s %s",ts,$3,$4,substr($load,1,length($load)-1);
   prev_ts=ts;
   }
   /Queries/{
   printf fmt,($2-Queries)/diff;
   Queries=$2
   }
   ' "$@"
```
{% asset_img 2021-04-22-23-01-35.png %}

### 基准测试中容易忽略的问题

- 使用生产环境数据时只使用了部分数据
推荐: 使用数据库完全备份来测试.
- 在多用户场景中，只做了单用户的测试
推荐: 使用多线程并发测试
- 在单服务器上测试分布式应用
推荐: 使用相同架构进行测试
- 反复执行同一查询
容易缓存命中，无法反映真实查询性能。

## 五、MySQL基准测试工具之mysqlslap

{% asset_img 2021-04-22-23-06-41.png %}

### 常用的基准测试工具介绍

MySQL基准测试工具之  `mysqlslap`

**下载及安装:**

MySQL服务器自带的基准测试工具，随MySQL一起安装

**特点:**
  - 可以模拟服务器负载，并输出相关统计信息
  - 可以指定也可以自动生成查询语句

**常用参数说明:**

`--auto-generate-sql`
由系统生成SQL脚本进行测试

`--auto-generate-sql-add-autoincrement`
在生成的表中增加自增ID

`--auto-generate-sql-load-type`
指定测试中使用的查询类型

`--auto-generate-sql-write-number`
指定初始化数据时生成的数据量

`--concurrency`
指定并发线程的数量

`--engine`
指定要测试表的存储引擎，可以用逗号分隔多个存储引擎

`--no-drop`
指定不清理测试数据

`--iterations`
指定测试运行的次数


`--number-of-queries`
指定每一个线程执行的查询数量

`--debug-info`
指定输出额外的内存及CPU统计信息

`--number-int-cols`
指定测试表中包含INT类型列的数量

`--number-char-cols`
指定测试表中包含的varchar类型的数量

`--create-schema`
指定了用于执行测试的数据库的名字

`--query`
用于指定自定义SQL的脚本

`--only-print`
并不运行测试脚本，而是把生成的脚本打印出来


### 演示
```
mysqlslap --concurrency=1,50,100,200 --iterations=3 --number-int-cols=5 --number-char-cols=5 --auto-generate-sql --auto-generate-sql-add-autoincrement --engine=myisam,innodb --number-of-queries=10 --create-schema=sbtest
```

{% asset_img 2021-04-22-23-24-15.png %}

{% asset_img 2021-04-22-23-25-25.png %}

{% asset_img 2021-04-22-23-25-51.png %}

```
mysqlslap --concurrency=1,50,100,200 --iterations=3 --number-int-cols=5 --number-char-cols=5 --auto-generate-sql --auto-generate-sql-add-autoincrement --engine=myisam,innodb --number-of-queries=10 --create-schema=sbtest --only-print | more
```

{% asset_img 2021-04-22-23-27-59.png %}

```
mysqlslap --concurrency=1,50,100,200 --iterations=3 --number-int-cols=5 --number-char-cols=5 --auto-generate-sql --auto-generate-sql-add-autoincrement --engine=myisam,innodb --number-of-queries=10 --create-schema=sbtest --only-print > 1.sql
```

## 六、MySQL基准测试工具之sysbench

TODO: // 未完待续...













