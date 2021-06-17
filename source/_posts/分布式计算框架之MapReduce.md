---
title: 分布式计算框架之MapReduce
date: 2021-06-17 21:12:34
tags:
 - 大数据
categories:
 - 大数据
---

# 分布式计算框架之MapReduce

{% asset_img 2021-06-17-22-52-03.png %}

## MapReduce概述

- MapReduce是一种分布式计算模型，由Google提出，主要用于搜索领域，解决海量数据的计算问题.

- MR由两个阶段组成：Map和Reduce，用户只需要实现map()和reduce()两个函数，即可实现分布式计算，非常简单。

- 这两个函数的形参是key、value对，表示函数的输入信息。

## MR执行流程

{% asset_img 2021-06-17-21-14-27.png %}


## MapReduce原理

{% asset_img 2021-06-17-21-16-06.png %}

◆执行步骤：

 **1. map任务处理**

1.1 读取输入文件内容，解析成key、value对。对输入文件的每一行，解析成key、value对。每一个键值对调用一次map函数。
1.2 写自己的逻辑，对输入的key、value处理，转换成新的key、value输出。
1.3 对输出的key、value进行分区。
1.4 对不同分区的数据，按照key进行排序、分组。相同key的value放到一个集合中。
1.5 (可选)分组后的数据进行归约。

**2.reduce任务处理**

2.1 对多个map任务的输出，按照不同的分区，通过网络copy到不同的reduce节点。
2.2 对多个map任务的输出进行合并、排序。写reduce函数自己的逻辑，对输入的key、value处理，转换成新的key、value输出。
2.3 把reduce的输出保存到文件中。

{% asset_img 2021-06-17-22-54-26.png %}

## 例子：实现WordCountApp

文件内容:

```
hello you
hello me
```

`1.1` 读取输入文件内容，解析成key、value对。对输入文件的每一行，解析成key、value对。每一个键值对调用一次map函数。

`执行结果: `

​		解析成2个<k,v>，分别是<0, hello you><10, hello me>。调用2次map函数。



`1.2` 覆盖map()，接收1.1产生的<k,v>，进行处理，转换为新的<k,v>输出

```
public void map(k, v, ctx){
   String[] splited = v.split("\t");
   for(String word : splited){
      ctx.write(word, 1);
   }
}

```

`1.3` 对1.2输出的<k,v>进行分区。默认分为1个区。

​		

`1.4` 对不同分区中的数据进行排序(按照k)、分组。分组指的是相同key的value放到一个集合中。

```
map输出后的数据是:<hello,1>,<you,1>,<hello,1>,<me,1>
排序后是:<hello,1>,<hello,1>,<me,1>,<you,1>
分组后是:<hello,{1,1}>,<me,{1}>,<you,{1}>

```

`1.5 (可选)` 对分组后的数据进行规约。



`2.1` 多个map任务的输出，按照不同的分区，通过网络copy到不同的reduce节点上。

`2.2` 对多个map的输出进行合并、排序。覆盖reduce函数，接收的是分组后的数据，实现自己的业务逻辑，处理后，产生新的<k,v>输出。

```
//reduce函数被调用的次数是3
public void reduce(k,vs, ctx){
    long sum = 0L;
    for(long times : vs){
        sum += times;    
    }  
    ctx.write(k, sum);
}

```

`2.3` 对reduce输出的<k,v>写到hdfs中。

```
hello   2
me      1
you     1

```





## map、reduce键值对格式

{% asset_img 2021-06-17-21-18-40.png %}

## Hadoop MapReduce Framework Architecture

{% asset_img 2021-06-17-21-23-14.png %}



| 组件        | 描述                                                         |
| ----------- | ------------------------------------------------------------ |
| JobTracker  | 负责接收用户提交的作业，负责启动、跟踪任务执行。
JobSubmissionProtocol是JobClient与JobTracker通信的接口。
InterTrackerProtocol是TaskTracker与JobTracker通信的接口。 |
| TaskTracker | 负责执行任务。                                               |
| JobClient   | 是用户作业与JobTracker交互的主要接口。
负责提交作业的，负责启动、跟踪任务执行、访问任务状态和日志等。 |

