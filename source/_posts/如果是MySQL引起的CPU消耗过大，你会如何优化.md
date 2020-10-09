---
title: 如果是MySQL引起的CPU消耗过大，你会如何优化?
date: 2020-09-30 11:15:53
tags:
  - 性能优化
  - MySQL
  - 转载
categories:
  - 性能调优专栏
---

作者：jiaxin_12

来源：rrd.me/gtrAA

--

## 谁在消耗 cpu?

用户+系统+IO 等待+软硬中断+空闲

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy82ZnVUM2VtV0k1S2pZbmhFaWFkMVRpYURTeUJJQXA1S1N1Zkx2RDY3eFFpYmljaWJROW9kRllnYXI1VURLWDNqNTNvTkY5S1h5ZmRyZTg3Z2NsZ0hBdmliOXQ3QS82NDA?x-oss-process=image/format,png)

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy82ZnVUM2VtV0k1S2pZbmhFaWFkMVRpYURTeUJJQXA1S1N1bUJHU0tHc283S0tGN3BERWtUQWtFdlJVQ1hxcEhIdzBWbTRpYWpMZUk0aGc1Y0k2ck1LaFR6QS82NDA?x-oss-process=image/format,png)

--

## 祸首是谁？

### 用户

用户空间 CPU 消耗，各种逻辑运算

> 正在进行大量 tps
>
> 函数/排序/类型转化/逻辑 IO 访问...

用户空间消耗大量 cpu，产生的系统调用是什么？那些函数使用了 cpu 周期？

### IO 等待

等待 IO 请求的完成

> 此时 CPU 实际上空闲

如 vmstat 中的 wa 很高。但 IO 等待增加，wa 也不一定会上升（请求 I/O 后等待响应，但进程从核上移开了）

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL3N6X21tYml6X3BuZy9IVjR5VEk2UGpiS3YyQmFtV0lPTmpUSVpWc214aWF5M2FKdG1RWDhXTXlyVmdGSXhEbEU0Ynh0aWFmcm9kTGlhVzljNk5zTTNFckdRelg5OHp4Qm5rejF6dy82NDA?x-oss-process=image/format,png)

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL3N6X21tYml6X3BuZy9IVjR5VEk2UGpiS3YyQmFtV0lPTmpUSVpWc214aWF5M2F3OTZ0UnJ3MnRPNGRNZ1dHTGxORXJnNk5XSUVPS3A1WHoxUjJPbm9TOTd5WmU1ZFNKeGt2cUEvNjQw?x-oss-process=image/format,png)

### 产生影响

用户和 IO 等待消耗了大部分 cpu

- 吞吐量下降（tps）

- 查询响应时间增加

- 慢查询数增加

- 对 mysql 的并发陡增，也会产生上诉影响

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy82ZnVUM2VtV0k1S2pZbmhFaWFkMVRpYURTeUJJQXA1S1N1bEZOcmNVSVVOZFA1TUhzWTFWS3UyVGJZckJWZkVhM0FrTmx3R0pQWDdlNW9ubFhHb09lTXVBLzY0MA?x-oss-process=image/format,png)

--

## 如何减少 CPU 消耗？

### 减少等待

减少 IO 量

SQL/index，使用合适的索引减少扫描的行数（需平衡索引的正收益和维护开销，空间换时间）

提升 IO 处理能力

加 cache/加磁盘/SSD

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy82ZnVUM2VtV0k1S2pZbmhFaWFkMVRpYURTeUJJQXA1S1N1b1lwRXpuMXVjS2lhTXdlUlBnVEVhVmNQd3lOOWY4eU5SVEtvcjBEbGFmcUxEa01LNFRSdWdkdy82NDA?x-oss-process=image/format,png)

### 减少计算

减少逻辑运算量

- 避免使用函数，将运算转移至易扩展的应用服务器中 如 substr 等字符运算，dateadd/datesub 等日期运算，abs 等数学函数

- 减少排序，利用索引取得有序数据或避免不必要排序 如 union all 代替 union，order by 索引字段等

- 禁止类型转换，使用合适类型并保证传入参数类型与数据库字段类型绝对一致 如数字用 tiny/int/bigint 等，必需转换的在传入数据库之前在应用中转好

- 简单类型，尽量避免复杂类型，降低由于复杂类型带来的附加运算。更小的数据类型占用更少的磁盘、内存、cpu 缓存和 cpu 周期

- ....

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL3N6X21tYml6X3BuZy9IVjR5VEk2UGpiS3YyQmFtV0lPTmpUSVpWc214aWF5M2E0dVVuUVhZdEV1VXRzM0VveDdRd3prZjlQd0Zra1RteUtoVDM5bGJjaWM5SXdTVEo5cklRVmt3LzY0MA?x-oss-process=image/format,png)

减少逻辑 IO 量

- index，优化索引，减少不必要的表扫描 如增加索引，调整组合索引字段顺序，去除选择性很差的索引字段等等

- table，合理拆分，适度冗余 如将很少使用的大字段拆分到独立表，非常频繁的小字段冗余到"引用表"

- SQL，调整 SQL 写法，充分利用现有索引，避免不必要的扫描，排序及其他操作 如减少复杂 join，减少 order by，尽量 union all，避免子查询等

- 数据类型，够用就好，减少不必要使用大字段 如 tinyint 够用就别总是 int，int 够用也别老 bigint，date 够用也别总是 timestamp

- ....

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy82ZnVUM2VtV0k1S2pZbmhFaWFkMVRpYURTeUJJQXA1S1N1cjFzdnRIS0toS1k1V0lrbTFReGxyY0VaNlhNekJpY1RyaWNoS2ljSzFXZ1M3UVVzc0c5MTRxdVN3LzY0MA?x-oss-process=image/format,png)

减少 query 请求量（非数据库本身）

- 适当缓存，降低缓存数据粒度，对静态并被频繁请求的数据进行适当的缓存 如用户信息，商品信息等

- 优化实现，尽量去除不必要的重复请求 如禁止同一页面多次重复请求相同数据的问题，通过跨页面参数传递减少访问等

- 合理需求，评估需求产出比，对产出比极端底下的需求合理去除

- ....

升级 cpu 若经过减少计算和减少等待后还不能满足需求，cpu 利用率还高 T_T 是时候拿出最后的杀手锏了，升级 cpu，是选择更快的 cpu 还是更多的 cpu 了？

- 低延迟（快速响应），需要更快的 cpu（每个查询只能使用一个 cpu）

- 高吞吐，同时运行很多查询语句，能从多个 cpu 处理查询中收益

--

## 参考

- 《高性能 MySQL》

- 《图解性能优化》

- 大部分整理自《MySQL Tuning For CPU Bottleneck》

有道无术，术可成；有术无道，止于术
