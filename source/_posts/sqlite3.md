---
title: sqlite3
urlname: sqlite3
top: false
categories:
  - 存储
tags:
  - sqlite3
author: jesonlin
date: 2022-12-09 15:09:43
update: 2022-12-09 15:09:43
---

SQLite 是一个软件库，实现了自给自足的、无服务器的、零配置的、事务性的 SQL 数据库引擎。SQLite 是在世界上最广泛部署的 SQL 数据库引擎。SQLite 源代码不受版权限制。

<!-- more -->

## SQLite 命令
1. DDL - 数据定义语言
    ```
    CREATE，ALTER，DROP
    ```
2. DML - 数据操作语言
    ```
    INSERT，UPDATE，DELETE
    ```
3. DQL - 数据查询语言
    ```
    SELECT
    ```

## 数据库操作
1. 创建数据库
    ```
    <!-- 将会在当前目录下创建一个文件testDB.db -->
    sqlite3 testDB.db 
    ```
2. 显示数据库
    ```
    .databases
    ```
3. 导出数据库
    ```
    sqlite3 testDB.db .dump > testDB.sql
    ```
4. 导入到数据库
    ```
    sqlite3 testDB.db < testDB.sql
    ```


## 数据表操作
1. 创建数据表
    ```
    create table if not exists tb_config_filter(id integer primary key autoincrement, instanceid varchar(255) default '', instancename varchar(255) default '', metricName varchar(255) default '', metricsshowname varchar(255) default '', metriclimit text default '');
    ```
2. 显示数据表
    ```
    .tables
    ```
3. 删除表
    ```
    DROP TABLE [TABLE_NAME];
    ```
4. 显示建表语句
    ```
    .schema [TABLE_NAME]
    ```
5. 插入表数据
    ```
    INSERT INTO TABLE_NAME ([COLUMN1],...,[COLUMNN]) VALUES ([VALUE1],...,[VALUEN]);
    <!-- 自增id字段值可用null替代或者不写 -->
    insert into tb_config_filter(id, instancename) values(null, 'cdb_yunying1_dev');
    insert into tb_config_filter(metricsshowname) values('CPU利用率');
    ```
6. 删除表数据
    ```
    DELETE FROM table_name WHERE [CONDITION];
    <!-- 删除单条 -->
    delete from tb_config_filter where id=2;
    <!-- 删除所有数据 -->
    delete from tb_config_filter 
    ```
7. 更新表数据
    ```
    UPDATE table_name SET column1 = value1, column2 = value2...., columnN = valueN WHERE [CONDITION];
    <!-- 更新自增id -->
    UPDATE sqlite_sequence SET seq = [下一个自增ID] WHERE name = [TABLE_NAME]; 
    ```
8. 查询表数据
    ```
    SELECT [COLUMN1], ..., [COLUMNN] FROM [TABLE_NAME] WHERE [CONDITION];
    ```
9. 格式化显示
    ```
    <!-- 显示当前设置 -->
    .show
    <!-- 显示字段名称 -->
    .header on
    <!-- 按列格式化显示，每列字段值长度有限，会被截断 -->
    .mode column
    <!-- 设置列宽度 -->
    .width 60
    <!-- 按行格式化显示 -->
    .mode line
    <!-- 按csv格式化显示 -->
    .mode csv
    <!-- 空按NULL显示 -->
    .nullvalue NULL
    <!-- 输出到文件/屏幕输出 -->
    .output [FILENAME]/stdout    
    <!-- 所有表信息查询 -->
    SELECT * FROM sqlite_master;
    <!-- 会有一个表sqlite_sequence记录表名对应自增id -->
    ```

## 索引操作
1. 增加索引
    ```
    <!-- 单列索引 -->
    CREATE INDEX [INDEX_NAME] ON [TABLE_NAME] ([COLUMN_NAME]);
    <!-- 唯一索引 -->
    CREATE UNIQUE INDEX [INDEX_NAME] ON [TABLE_NAME] ([COLUMN_NAME]);
    <!-- 组合索引 -->
    CREATE INDEX [INDEX_NAME] ON [TABLE_NAME] ([COLUMN1_NAME], [COLUMN2_NAME]);
    ```
2. 删除索引
    ```
    DROP INDEX index_name
    ```
3. 什么情况下要避免使用索引？
    虽然索引的目的在于提高数据库的性能，但这里有几个情况需要避免使用索引。使用索引时，应重新考虑下列准则：  
    1. 索引不应该使用在较小的表上。
    2. 索引不应该使用在有频繁的大批量的更新或插入操作的表上。
    3. 索引不应该使用在含有大量的 NULL 值的列上。
    4. 索引不应该使用在频繁操作的列上。


## python
```
import sqlite3
conn = sqlite3.connect('test.db')
cursor = conn.cursor()
res = cursor.execute("create table if not exists tb_config_filter(id integer primary key autoincrement, instanceid varchar(255) default '', instancename varchar(255) default '', metricName varchar(255) default '', metricsshowname varchar(255) default '', metriclimit text default '');")
print(res)
res = cursor.execute("CREATE INDEX idx_instanceid ON tb_config_filter (instanceid);")
print(res)
conn.commit()
cursor.close()
conn.close()
```


## REFERENCE
[使用sqlite](https://www.liaoxuefeng.com/wiki/1016959663602400/1017801751919456)
[SQLite 教程](https://www.runoob.com/sqlite/sqlite-tutorial.html)