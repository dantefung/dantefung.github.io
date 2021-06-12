---
title: Java之NIO
date: 2021-04-23 15:56:36
tags:
  - NIO
---


# Java之NIO



## 基础概念

### 生活例子:

**同步 ：** 自己亲自出马持银行卡到银行取钱（使用同步IO时，Java自己处理IO读写）。

**异步 ：** 委托一小弟拿银行卡到银行取钱，然后给你（使用异步IO时，Java将IO读写委托给OS处理，需要将数据缓冲区地址和大小传给OS(银行卡和密码)，OS需要支持异步IO操作API）。

**阻塞 ：** ATM排队取款，你只能等待（使用阻塞IO时，Java调用会一直阻塞到读写完成才返回）。

**非阻塞 ：** 柜台取款，取个号，然后坐在椅子上做其它事，等号广播会通知你办理，没到号你就不能去，你可以不断问大堂经理排到了没有，大堂经理如果说还没到你就不能去（使用非阻塞IO时，如果不能读写Java调用会马上返回，当IO事件分发器会通知可读写时再继续进行读写，不断循环直到读写完成）。


### 操作系统层面的理解:

Linux内核将所有的外部设备都看作一个文件来操作，对一个文件的读写都会调用内核的系统命令，返回一个文件描述符(File Descriptor, 简称fd, 有些地方也叫做句柄)。

而对于一个socket的读写也看作是文件读写，同样会返回一个文件描述符， 称作socket fd。

*fd* 是一个数数字， 它指向内核中的一个数据结构体。 Linux系统中， *fd* 是有打开限制的。

**1、阻塞I/O（blocking IO）**

应用程序调用一个IO函数，导致应用程序阻塞，如果数据已经准备好，从内核拷贝到用户空间，否则一直等待下去。

一个典型的读操作流程大致如下图, 当用户进程调用read这个系统调用时。

kernel 就开始了IO的第一阶段:<font color='blue'> *准备数据, 就是数据被拷贝到内核缓冲区中的一个过程.*</font>

等数据到操作系统的内核缓冲区了, 就到了第二阶段: <font color='blue'>*将数据从内核缓冲区拷贝到用户内存，然后kernel返回结果，用户进程才会接触block状态，重新运行起来.*</font>

<font color="red">blocking IO的特点就是在IO执行的两个阶段用户进程都会block住。</font>


{% asset_img 2021-04-23-16-37-21.png %}

**小结:**

   阻塞的两个部分:
   1、CPU把数据从磁盘读到内核缓冲区。
   2、CPU把数据从内核缓冲区拷贝到用户缓冲区。

**2、非阻塞IO（nonblocking IO）**

**关键字:** 主动轮询

非阻塞IO模型，我们把一个套接口设置为非阻塞就是告诉内核，当所有请求的IO操作无法完成时， 不要将进程睡眠， 而是返回一个错误。

这样， 我们的IO操作函数将不断的测试数据是否已经准备好，如果没有准备好，继续测试，直到数据准备好为止。在这个不断测试的过程中， <font color='red'>会大量的占用CPU时间。</font>

当用户进程发出read操作时， 如果kernel钟数据还没准备好，那么并不会block用户进程，而是立即返回error，用户进程判断结果是error，就知道数据还没有准备好， 用户可以再次发read,知道kernel中数据准备好，并且用户再一次发read操作，产生system call, 那么kernel马上将数据拷贝到用户内存， 然后返回。

所以，<font color='red'>nonblocking IO的特点是用户进程需要不断主动询问kernel数据准备好了没有。</font>


{% asset_img 2021-04-25-10-16-55.png %}

**3、IO多路复用模型(IO multiplexing)**

**关键字：** 事件驱动机制、event notification mechansim

非阻塞情况下无可用数据时，应用程序每次轮询内核看数据是否准备好了也耗费CPU，能否不让它轮询，当内核缓冲区数据准备好了，以`事件通知的机制`告知应用进程数据准备好了呢？应用进程在没有收到数据准备好的事件通知信号时可以忙写其他的工作。此时`IO多路复用`就派上用场了。


{% asset_img 2021-04-25-10-08-56.png %}

这里最后一次 read 调用获取数据的过程，是一个同步的过程，是需要等待的过程。这里的同步指的是<font color='green'>内核态的数据拷贝到用户程序的缓存区这个过程。</font>


**补充知识:**

I/O multiplexing

In computing, I/O multiplexing can also be used to refer to the concept of processing multiple input/output events from a single event loop, with system calls like poll[1] and select (Unix).[2]

> NOTE: 如何来理解multiplexing？multiplexing的关键在于many和one，下面是一些例子:
>1、在telecommunications中many 是multiple analog or digital signals ，one是 combined into one signal over a shared medium 。
>
>many logical and one physical ；
>
>2、在 computing的 **I/O multiplexing**中，many是<font color='orange'>multiple input/output events</font> 而one则是<font color='orange'>a single event loop object</font>；
>
> many IO event and one event loop object；
>
>显然multiplexing的目标在于实现性能的提升，资源的节省；
>
> 在现实application中，都需要有reverse operation: demultiplexing，**demultiplexing**其实非常类似于dispatch

`I/O multiplexing` means what it says - allowing the programmer to examine and block on multiple I/O streams (or other "synchronizing" events), being notified whenever any one of the streams is active so that it can process data on that stream.

The advantage of IO Multiplexing is that it allows blocking on multiple resources simultaneously(同时地), without needing to use polling (which wastes CPU cycles) or multithreading (which can be difficult to deal with, especially if threads are introduced into an otherwise sequential app only for the purpose of pending on multiple descriptors).

> NOTE:
> 以上讲的是IO多路复用的特点，所能做到的事。那么它是如何实现的呢?
>
> Event Demultiplexer and Event Handler
>
>一般地,I/O多路复用机制都依赖于一个事件**多路分离器(Event Demultiplexer)。**分离器对象**可将来自事件源的I/O事件分离出来，并分发到对应的**read/write事件处理器(Event Handler)(或回调函数)。开发人员预先注册需要处理的事件及其事件处理器（或回调函数）；事件分离器负责将请求事件传递给事件处理器。






|IO类型|描述|
|---|---|
|阻塞IO|一个线程只能处理一个IO流事件, 要想同时处理多个IO流事件要么多线程要么多进程。|
|非阻塞IO|可以一个线程处理多个流事件，只要不停地轮询所有流事件即可，当然这个方式也不好，当大多数据流没数据时，也是会大量浪费CPU资源。|




## Java对BIO、NIO、AIO的支持：

Java BIO ： 同步并阻塞，服务器实现模式为一个连接一个线程，即客户端有连接请求时服务器端就需要启动一个线程进行处理，如果这个连接不做任何事情会造成不必要的线程开销，当然可以通过线程池机制改善。

Java NIO ： 同步非阻塞，服务器实现模式为一个请求一个线程，即客户端发送的连接请求都会注册到多路复用器上，多路复用器轮询到连接有I/O请求时才启动一个线程进行处理。

Java AIO(NIO.2) ： 异步非阻塞，服务器实现模式为一个有效请求一个线程，客户端的I/O请求都是由OS先完成了再通知服务器应用去启动线程进行处理.

## BIO、NIO、AIO适用场景分析:
BIO方式适用于连接数目比较小且固定的架构，这种方式对服务器资源要求比较高，并发局限于应用中，JDK1.4以前的唯一选择，但程序直观简单易理解。

NIO方式适用于连接数目多且连接比较短（轻操作）的架构，比如聊天服务器，并发局限于应用中，编程比较复杂，JDK1.4开始支持。

AIO方式使用于连接数目多且连接比较长（重操作）的架构，比如相册服务器，充分调用OS参与并发操作，编程比较复杂，JDK7开始支持。

> 对于初学者来说，同步并阻塞、同步非阻塞、异步非阻塞并不容易理解。

那么网络上有一个段子。。
老张爱喝茶，废话不说，煮开水。
出场人物：老张，水壶两把（普通水壶，简称水壶；会响的水壶，简称响水壶）。
1 老张把水壶放到火上，立等水开。（同步阻塞）老张觉得自己有点傻
2 老张把水壶放到火上，去客厅看电视，时不时去厨房看看水开没有。（同步非阻塞）老张还是觉得自己有点傻，于是变高端了，买了把会响笛的那种水壶。水开之后，能大声发出嘀~~~~的噪音。
3 老张把响水壶放到火上，立等水开。（异步阻塞）老张觉得这样傻等意义不大
4 老张把响水壶放到火上，去客厅看电视，水壶响之前不再去看它了，响了再去拿壶。（异步非阻塞）老张觉得自己聪明了。

所谓同步异步，只是对于水壶而言。普通水壶，同步；响水壶，异步。虽然都能干活，但响水壶可以在自己完工之后，提示老张水开了。这是普通水壶所不能及的。同步只能让调用者去轮询自己（情况2中），造成老张效率的低下。所谓阻塞非阻塞，仅仅对于老张而言。

立等的老张，阻塞；看电视的老张，非阻塞。情况1和情况3中老张就是阻塞的，媳妇喊他都不知道。虽然3中响水壶是异步的，可对于立等的老张没有太大的意义。所以一般异步是配合非阻塞使用的，这样才能发挥异步的效用。
——来源网络，作者不明。








## Reference:

- [Java NIO（一）探索](https://www.jianshu.com/p/30c01fbcd632)
- [阻塞、非阻塞、多路复用、同步、异步、BIO、NIO、AIO 一锅端](https://mp.weixin.qq.com/s/17S9Io1dIS_fihfLk5bsAQ)
- [Multiplexing](https://dengking.github.io/Linux-OS/Programming/IO/IO-model/IO-multiplexing/What-is-multiplexing/)