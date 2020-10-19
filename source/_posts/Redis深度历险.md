---
title: Redis深度历险
date: 2020-10-19 23:07:21
tags:
 - redis
 - 分布式缓存
categories:
 - 分布式技术
---

# Redis深度历险

## Redis基础数据结构
Redis 有 5 种基础数据结构，分别为：`string (字符串)`、`list (列表)`、`set (集合)`、`hash (哈希)` 和 `zset (有序集合)`。

### String
Redis 的字符串是动态字符串，是可以修改的字符串，内部结构实现上类似于 Java 的 ArrayList，采用预分配冗余空间的方式来减少内存的频繁分配，如图中所示，内部为当前字符串实际分配的空间 capacity 一般要高于实际字符串长度 len。当字符串长度小于 1M 时，扩容都是加倍现有的空间，如果超过 1M，扩容时一次只会多扩 1M 的空间。需要注意的是字符串最大长度为 512M。

- 键值对
- 批量键值对
- 过期和set命令扩展
<p class="note note-primary">
 setex name 5 codehole  # 5s 后过期，等价于 set+expire<br>
 setnx name codehole  # 如果 name 不存在就执行 set 创建
</p>
- 计数
<p class="note note-primary">如果 value 值是一个整数，还可以对它进行自增操作。自增是有范围的，它的范围是 signed long 的最大最小值，超过了这个值，Redis 会报错。</p>

### list
Redis 的列表相当于 Java 语言里面的 LinkedList，注意它是链表而不是数组。这意味着 list 的插入和删除操作非常快，时间复杂度为 O(1)，但是索引定位很慢，时间复杂度为 O(n)，这点让人非常意外。

当列表弹出了最后一个元素之后，该数据结构自动被删除，内存被回收。

{% note danger %}
Redis 的列表结构常用来做异步队列使用。将需要延后处理的任务结构体序列化成字符串塞进 Redis 的列表，另一个线程从这个列表中轮询数据进行处理。
{% endnote %}

- 右边进左边出：队列
- 右边进右边出：栈
- 慢操作
<p class="note note-primary">
lindex 相当于 Java 链表的get(int index)方法，它需要对链表进行遍历，性能随着参数index增大而变差。
ltrim 和字面上的含义不太一样，个人觉得它叫 lretain(保留) 更合适一些，因为 ltrim 跟的两个参数start_index和end_index定义了一个区间，在这个区间内的值，ltrim 要保留，区间之外统统砍掉。我们可以通过ltrim来实现一个定长的链表，这一点非常有用。
index 可以为负数，index=-1表示倒数第一个元素，同样index=-2表示倒数第二个元素。
</p>
- 快速列表
{% asset_img 2020-10-19-23-42-41.png %}
{% note primary %}
Redis 将链表和 ziplist 结合起来组成了 quicklist。也就是将多个 ziplist 使用双向指针串起来使用。这样既满足了快速的插入删除性能，又不会出现太大的空间冗余。
{% endnote %}

### hash(字典)

Redis 的字典相当于 Java 语言里面的 HashMap，它是无序字典。内部实现结构上同 Java 的 HashMap 也是一致的，同样的数组 + 链表二维结构。第一维 hash 的数组位置碰撞时，就会将碰撞的元素使用链表串接起来。

{% asset_img 2020-10-19-23-46-48.png %}

{% note primary %}
1.Redis 的字典的值只能是字符串
2.Java 的 HashMap 在字典很大时，rehash 是个耗时的操作，需要一次性全部 rehash。
3.Redis 为了高性能，不能堵塞服务，所以采用了渐进式 rehash 策略。
渐进式 rehash 会在 rehash 的同时，保留新旧两个 hash 结构，查询时会同时查询两个 hash 结构，然后在后续的定时任务中以及 hash 操作指令中，循序渐进地将旧 hash 的内容一点点迁移到新的 hash 结构中。当搬迁完成了，就会使用新的hash结构取而代之。
4.hash 也有缺点，hash 结构的存储消耗要高于单个字符串，到底该使用 hash 还是字符串，需要根据实际情况再三权衡。
5.同字符串对象一样，hash 结构中的单个子 key 也可以进行计数，它对应的指令是 hincrby，和 incr 使用基本一样。
{% endnote %}

### set (集合)

{% asset_img 16458e2da04f1a2d.gif %}

{% note primary %}
1.Redis 的集合相当于 Java 语言里面的 HashSet，它内部的键值对是无序的唯一的。它的内部实现相当于一个特殊的字典，字典中所有的 value 都是一个值NULL。

2.当集合中最后一个元素移除之后，数据结构自动删除，内存被回收。
{% endnote %}

### zset (有序集合)
{% note primary %}
1、zset 可能是 Redis 提供的最为特色的数据结构，它也是在面试中面试官最爱问的数据结构。它类似于 Java 的 SortedSet 和 HashMap 的结合体，一方面它是一个 set，保证了内部 value 的唯一性，另一方面它可以给每个 value 赋予一个 score，代表这个 value 的排序权重。它的内部实现用的是一种叫做「跳跃列表」的数据结构。

2、zset 中最后一个 value 被移除后，数据结构自动删除，内存被回收。
{% endnote %}

**跳跃列表**














