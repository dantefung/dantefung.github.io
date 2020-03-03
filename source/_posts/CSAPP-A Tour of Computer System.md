

title: A Tour of Computer System
author: dantefung
tags:

- CS

categories: []
date: 2020年3月3日 10:34:01



----



计算机的五大基础部件是：存储器、控制器、运算器、输入和输出设备。

存储器分为: 磁盘和内存。

> Note:
> 
> 磁盘和内存的区别:
> 
> 磁盘和内存都是存储设备。
> 
> `内存`通过电流来实现存储。
> 
> `磁盘`通过磁记录技术来实现存储。

## 主存(Main Memory)

#### 一、什么是主存?

一般内存指的就是主存（负责存储CPU中运行的程序和数据）。

**作用:**

存放CPU中的运算数据，以及与硬盘等外部存储设备交换的数据。

只要计算机在运行中，CPU就会把需要运算的数据调到主存中进行运算，当运算完成后CPU再将结果传送出来。

### 二、主存的物理结构

> Physically,main memory consists of a collection of `dynamic random access memory(DRAM)` chips.

![](https://imgkr.cn-bj.ufileos.com/f9f48492-ffad-4df8-8f34-68b3ac278dc2.png)

#### 2.1 IC电路中主要的三种存储器

内存的内部是由各种`IC电路`组成的，它的种类很庞大，但是主要分为三种存储器:

**随机存储器(RAM):**  内存中最重要的一种，表示既可以从中读取数据，也可以写入数据。当机器关闭时，内存中的信息会丢失。

**只读存储器(ROM)：** ROM一般只能用于数据的读取，    不能写入数据，但是当机器停电的时候，这些数据不会丢失。

**高速缓存(Cache):**  Cache也是我们经常见到的，它分为一级缓存（L1 Cache）、二级缓存(L2 Cache)、三级缓存(L3 Cache)。这些数据，它位于内存和CPU之间，是一个读写速度比内存更快的存储器。当CPU写入数据时，这些数据也会被写入高速缓存中。当CPU需要读取数据时，会直接从高速缓存中直接读取，当然，如需要的数据在Cache中没有，CPU会再去读取内存中的数据。

#### 2.2 内存IC结构及其数据存储

`内存 IC` 是一个完整的结构，它内部也有电源、地址信号、数据信号、控制信号和用于寻址的 IC 引脚来进行数据的读写。下面是一个虚拟的 IC 引脚示意图

![](C:\Users\fenghaolin\AppData\Roaming\marktext\images\2020-03-02-15-18-29-image.png)

`D0 - D7` 表示的是数据信号，也就是说，一次可以输入输出 8 bit = 1 byte 的数据。

`A0 - A9` 是地址信号共十个，表示可以指定 00000 00000 - 11111 11111 共 2 的 10次方 = 1024个地址。

每个地址都会存放 1 byte 的数据，因此我们可以得出内存 IC 的容量就是 1 KB。

> Logically,memory is organized as a linear array of bytes, each with its own unique address(array index) starting at zero.
