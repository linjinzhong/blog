---
title: mysql-内存
urlname: mysql-内存
top: false
categories:
    - 存储
tags:
    - mysql
author: jesonlin
date: 2022-12-08 20:07:30
update: 2022-12-08 20:07:30
---

Innodb 存储引擎设计了一个缓冲池（Buffer Pool），来提高数据库的读写性能。

<!-- more -->

# 为什么要有 Buffer Pool

<img src="./缓冲池.webp" width = "50%" height = "50%" alt="缓冲池" align=center />

MySQL 的数据是存储在磁盘里的，但是也不能每次都从磁盘里面读取数据，这样性能是极差的。

## 有了缓冲池后：
当读取数据时，如果数据存在于 Buffer Pool 中，客户端就会直接读取 Buffer Pool 中的数据，否则再去磁盘中读取。
  
当修改数据时，首先是修改 Buffer Pool 中数据所在的页，然后将其页设置为脏页，最后由后台线程将脏页写入到磁盘。

## Buffer Pool 有多大？
    
Buffer Pool 是在 MySQL 启动的时候，向操作系统申请的一片连续的内存空间，默认配置下Buffer Pool 只有 128MB 。

可以通过调整 innodb_buffer_pool_size 参数来设置 Buffer Pool 的大小，一般建议设置成可用物理内存的 60%~80%。

## Buffer Pool 缓存什么？
    
InnoDB 会把存储的数据划分为若干个「页」，以页作为磁盘和内存交互的基本单位，一个页的默认大小为 16KB。因此，Buffer Pool 同样需要按「页」来划分。

Buffer Pool 中的页就叫做缓存页。此时这些缓存页都是空闲的，之后随着程序的运行，才会有磁盘上的页被缓存到 Buffer Pool 中。

所以，MySQL 刚启动的时候，你会观察到使用的虚拟内存空间很大，而使用到的物理内存空间却很小，这是因为只有这些虚拟内存被访问后，操作系统才会触发缺页中断，接着将虚拟地址和物理地址建立映射关系。

Buffer Pool 除了缓存「索引页」和「数据页」，还包括了 undo 页，插入缓存、自适应哈希索引、锁信息等等。

<img src="./bufferpool内容.webp" width = "50%" height = "50%" alt="bufferpool内容" align=center />


# 如何管理 Buffer Pool？
## 如何管理空闲页？
<img src="./freelis.webp" width = "50%" height = "50%" alt="freelis" align=center />
    
Buffer Pool 是一片连续的内存空间，为了能够快速找到空闲的缓存页，可以使用链表结构，将空闲缓存页的「控制块」作为链表的节点，这个链表称为 Free 链表（空闲链表）。


## 如何管理脏页？
<img src="./Flush.webp" width = "50%" height = "50%" alt="Flush" align=center />

设计 Buffer Pool 除了能提高读性能，还能提高写性能，也就是更新数据的时候，不需要每次都要写入磁盘，而是将 Buffer Pool 对应的缓存页标记为脏页，然后再由后台线程将脏页写入到磁盘。

为了能快速知道哪些缓存页是脏的，于是就设计出 Flush 链表，它跟 Free 链表类似的，链表的节点也是控制块，区别在于 Flush 链表的元素都是脏页。


## 如何提高缓存命中率？
Buffer Pool 的大小是有限的，对于一些频繁访问的数据我们希望可以一直留在 Buffer Pool 中，而一些很少访问的数据希望可以在某些时机可以淘汰掉，从而保证 Buffer Pool 不会因为满了而导致无法再缓存新的数据，同时还能保证常用数据留在 Buffer Pool 中。
    
要实现这个，最容易想到的就是 LRU（Least recently used）算法。

该算法的思路是，链表头部的节点是最近使用的，而链表末尾的节点是最久没被使用的。那么，当空间不够了，就淘汰最久没被使用的节点，从而腾出空间。

Buffer Pool 里有三种页和链表来管理数据。

<img src="./bufferpoll_page.webp" width = "50%" height = "50%" alt="bufferpoll_page" align=center />

    Free Page（空闲页），表示此页未被使用，位于 Free 链表；
    Clean Page（干净页），表示此页已被使用，但是页面未发生修改，位于LRU 链表。
    Dirty Page（脏页），表示此页「已被使用」且「已经被修改」，其数据和磁盘上的数据已经不一致。当脏页上的数据写入磁盘后，内存数据和磁盘数据一致，那么该页就变成了干净页。脏页同时存在于 LRU 链表和 Flush 链表。

简单的 LRU 算法并没有被 MySQL 使用，因为简单的 LRU 算法无法避免下面这两个问题：
预读失效：
    
    程序是有空间局部性的，靠近当前被访问数据的数据，在未来很大概率会被访问到。MySQL 在加载数据页时，会提前把它相邻的数据页一并加载进来，目的是为了减少磁盘 IO。但是可能这些被提前加载进来的数据页，并没有被访问，相当于这个预读是白做了，这个就是预读失效。

    解决方法：
        让预读的页停留在 Buffer Pool 里的时间要尽可能的短，让真正被访问的页才移动到 LRU 链表的头部，从而保证真正被读取的热数据留在 Buffer Pool 里的时间尽可能长。

<img src="./young+old.webp" width = "50%" height = "50%" alt="young+old" align=center />

Buffer Pool 污染：
    
    当某一个 SQL 语句扫描了大量的数据时，在 Buffer Pool 空间比较有限的情况下，可能会将 Buffer Pool 里的所有页都替换出去，导致大量热数据被淘汰了，等这些热数据又被再次访问的时候，由于缓存未命中，就会产生大量的磁盘 IO，MySQL 性能就会急剧下降，这个过程被称为 Buffer Pool 污染。

    Buffer Pool 污染并不只是查询语句查询出了大量的数据才出现的问题，即使查询出来的结果集很小，也会造成 Buffer Pool 污染。比如索引失效导致的全表扫描。

    解决方法：
        像前面这种全表扫描的查询，很多缓冲页其实只会被访问一次，但是它却只因为被访问了一次而进入到 young 区域，从而导致热点数据被替换了。LRU 链表中 young 区域就是热点数据，只要我们提高进入到 young 区域的门槛，就能有效地保证 young 区域里的热点数据不会被替换掉。

        MySQL 是这样做的，进入到 young 区域条件增加了一个停留在 old 区域的时间判断。
            
        这个间隔时间是由 innodb_old_blocks_time 控制的，默认是 1000 ms。

        也就说，只有同时满足「被访问」与「在 old 区域停留时间超过 1 秒」两个条件，才会被插入到 young 区域头部，这样就解决了 Buffer Pool 污染的问题 。

        另外，MySQL 针对 young 区域其实做了一个优化，为了防止 young 区域节点频繁移动到头部。young 区域前面 1/4 被访问不会移动到链表头部，只有后面的 3/4被访问了才会。

## 脏页什么时候会被刷入磁盘？
InnoDB 的更新操作采用的是 Write Ahead Log 策略，即先写日志，再写入磁盘，通过 redo log 日志让 MySQL 拥有了崩溃恢复能力。

下面几种情况会触发脏页的刷新：
    
    当 redo log 日志满了的情况下，会主动触发脏页刷新到磁盘；

    Buffer Pool 空间不足时，需要将一部分数据页淘汰掉，如果淘汰的是脏页，需要先将脏页同步到磁盘；

    MySQL 认为空闲时，后台线程会定期将适量的脏页刷入到磁盘；

    MySQL 正常关闭之前，会把所有的脏页刷入到磁盘；

在我们开启了慢 SQL 监控后，如果你发现**「偶尔」会出现一些用时稍长的 SQL**，这可能是因为脏页在刷新到磁盘时可能会给数据库带来性能开销，导致数据库操作抖动。

如果间断出现这种现象，就需要调大 Buffer Pool 空间或 redo log 日志的大小。


# REFERENCE
[MySQL实战45讲](https://time.geekbang.org/column/intro/100020801)
[图解MySQL介绍](https://xiaolincoding.com/mysql/)