---
title: mysql-基础
urlname: mysql-基础
top: false
categories:
  - 存储
tags:
  - mysql
author: jesonlin
date: 2022-12-08 21:07:30
update: 2022-12-08 21:07:30
---

MySQL 执行一条查询/更新语句，期间发生了什么？

<!-- more -->

## MySQL 执行查询流程
0. mysql逻辑架构图
    <!-- ![mysql逻辑架构图](/mysql逻辑架构图.webp) -->
    <img src="./mysql逻辑架构图.webp" width = "50%" height = "50%" alt="update更新流程" align=center />

1. 连接器-建立连接，管理连接、校验用户身份
    1. 客户端跟服务端建立连接
        ```bash
        # 连接器负责跟客户端建立连接、获取权限、维持和管理连接。连接命令一般是这么写的：
        mysql -h$ip -P$port -u$user -p
        ```
    2. TCP 三次握手；
    3. 验证用户名和密码，权限固定在该连接上；
    4. 连接后状态
        ```bash
        # 没有后续动作处于空闲状态，即Command列显示sleep
        show processlist;

        # 太长没动静，根据参数wait_timeout时间断开链接，默认8小时

        # 建立链接比较复杂，尽量使用长连接

        # 因为执行过程中临时使用的内存是管理在连接对象里面的。所以长连接累积下来，可能导致内存占用太大。被系统强行杀掉（OOM），从现象看就是 MySQL 异常重启了。
        # 定期断开长连接或者执行较大操作后通过执行mysql_reset_connection来重新初始化连接资源。
        ```

2. 查询缓存-查询语句:查询结果
    1. mysql拿到一个查询请求后，会先查询缓存看看，key 是查询的语句，value 是查询的结果。
    2. 查询缓存的失效非常频繁，只要有对一个表的更新，这个表上所有的查询缓存都会被清空。对于更新压力大的数据库来说，查询缓存的命中率会非常低。
    3. 缓存“按需使用”的方式。将参数 query_cache_type 设置成 DEMAND，这样对于默认的 SQL 语句都不使用查询缓存。而对于你确定要使用查询缓存的语句，可以用 SQL_CACHE 显式指定，像下面这个语句一样：
        ```sql
        select sql_cache * from T where ID=2;
        ```
    MySQL 8.0 版本直接将查询缓存的整块功能删掉了，也就是说 8.0 开始彻底没有这个功能了。

3. 分析器-sql语句词法和语法解析构建语法树
    1. SQL语句解析，输入的是由多个字符串和空格组成的一条 SQL 语句，MySQL 需要识别出里面的字符串分别是什么，代表什么。
    2. 词法分析。
    3. 语法分析。

4. 执行器
    1. 预处理阶段-字段表校验、符号扩展
        检查表或字段是否存在；将 select * 中的 * 符号扩展为表上的所有列。
    2. 优化器-怎么做
        1. 在表里面有多个索引的时候，决定使用哪个索引；
        2. 在一个语句有多表关联（join）的时候，决定各个表的连接顺序。
        3. 覆盖索引，不回表，直接在二级索引就能查到结果（因为二级索引的 B+ 树的叶子节点的数据存储的是主键值）。同时查询主键索引的 B+ 树的成本会比查询二级索引的 B+ 的成本大（因为主键索引叶子节点存全量数据）。
    3. 执行器-具体执行。
        1. 主键索引查询
        2. 全表扫描
        3. 索引下推
            1. 联合索引当遇到范围查询 (>、<) 就会停止匹配。
            2. 将其他条件中的联合索引部分条件在引擎层判断，减少回表后在server层判断的次数。
            3. 二级索引在查询时的不进行回表操作，就能提高查询的效率，因为它将 Server 层部分负责的事情，交给存储引擎层去处理了。


## MySQL 执行更新流程
<!-- ![update 更新流程](/update更新流程.webp) -->
<img src="./update更新流程.webp" width = "50%" height = "50%" alt="update更新流程" align=center />


## 两阶段提交
![两阶段提交](/两阶段提交.png)

InnoDB在写redo log时，并不是一次性写完的，而有两个阶段，Prepare与Commit阶段，这就是"两阶段提交"的含义。

1. 为什么要写redo log?
    MySQL为了提升性能，引入了BufferPool缓冲池。查询数据时，先从BufferPool中查询，查询不到则从磁盘加载在BufferPool。

    每次对数据的更新，也不总是实时刷新到磁盘，而是先同步到BufferPool中，涉及到的数据页就会变成脏页。

    同时会启动后台线程，异步地将脏页刷新到磁盘中，来完成BufferPool与磁盘的数据同步。

    如果在某个时间，MySQL突然崩溃，则内存中的BufferPool就会丢失，剩余未同步的数据就会直接消失。

    虽然在更新BufferPool后，也写入了binlog中，但binlog并不具备crash-safe的能力。

    因为崩溃可能发生在写binlog后，刷脏前。在主从同步的情况下，从节点会拿到多出来的一条binlog。

2. 为什么要写两次redo log?
    如果只写一次，存在以下两种

        1. 先写binlog，再写redo log
            当前事务提交后，写入binlog成功，之后主节点崩溃。在主节点重启后，由于没有写入redo log，因此不会恢复该条数据。

            而从节点依据binlog在本地回放后，会相对于主节点多出来一条数据，从而产生主从不一致。

        2. 先写redo log，再写binlog
            当前事务提交后，写入redo log成功，之后主节点崩溃。在主节点重启后，主节点利用redo log进行恢复，就会相对于从节点多出来一条数据，造成主从数据不一致。

    因此，只写一次redo log与binlog，无法保证这两种日志在事务提交后的一致性。

3. 两阶段提交的情况下，如何实现崩溃恢复？
    首先比较重要的一点是，在写入redo log时，会顺便记录当前事务id。在写入binlog时，也会写入当前事务id。

        1. 如果在写入redo log之前崩溃，那么此时redo log与binlog中都没有，是一致的情况，崩溃也无所谓。

        2. 如果在写入redo log prepare阶段之后立马崩溃，之后会在崩溃恢复时，由于redo log没有被标记为commit。于是拿 redo log中事务id去bin log中查找，此时肯定找不到，立即执行回滚操作。

        3. 如果在写入bin log后立马崩溃，在恢复时，由redo log中的事务id可以找到对应的bin log，这个时候直接提交即可。

    总的来说，在崩溃恢复后，只要redo log不是处于commit阶段，那么就拿着redo log中的XID去binlog中寻找，找得到就提交，否则就回滚。



## REFERENCE
[MySQL实战45讲](https://time.geekbang.org/column/intro/100020801)
[图解MySQL介绍](https://xiaolincoding.com/mysql/)