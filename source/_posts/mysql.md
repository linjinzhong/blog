---
title: mysql
urlname: mysql
top: false
categories:
  - db
tags:
  - mysql
author: jesonlin
date: 2022-12-08 20:07:30
updated: 2022-12-08 20:07:30
---

MySQL是目前应用最广泛的开源关系数据库。

<!-- more -->


## 安装
1. centos7 安装MySQL
    ```
    <!-- 检查mariadb -->
    rpm -qa|grep mariadb
    <!-- 如有则按如下删除 -->
    rpm -e --nodeps mariadb-server
    rpm -e --nodeps mariadb
    rpm -e --nodeps mariadb-libs
    <!-- 检查mysql -->
    rpm -qa|grep mysql
    <!-- 存在则按如下删除 -->
    rpm -e --nodeps 【xxx】
    <!-- mysql yum源下载官网地址 -->
    https://dev.mysql.com/downloads/repo/yum/
    <!-- 根据自己系统版本选择 -->
    cat /etc/redhat-release
    <!-- centos7 选择如下 -->
    wget https://dev.mysql.com/get/mysql80-community-release-el7-7.noarch.rpm
    <!-- 安装mysql源 -->
    rpm -Uvh mysql80-community-release-el7-7.noarch.rpm
    <!-- 检查是否安装成功，是否有mysql-community.repo及 mysql-community-source.repo -->
    ll /etc/yum.repos.d/
    <!-- 查看源中的mysql版本 -->
    yum repolist all | grep mysql
    <!-- 切换mysql版本 -->
    yum-config-manager --disable mysql80-community
    yum-config-manager --enable mysql57-community
    <!-- 检查当前启用的mysql仓库 -->
    yum repolist enabled | grep mysql
    <!-- 安装mysql -->
    sudo yum -y install mysql-community-server
    <!-- 会安装如下 -->
    mysql-community-server
    mysql-community-client
    mysql-community-common
    mysql-community-libs
    numactl-libs
    <!-- 启动mysql -->
    sydo systemctl start mysqld.service
    <!-- 查看状态 -->
    sudo systemctl status mysqld.service
    <!-- 停止mysql -->
    sudo systemctl stop mysqld.service
    <!-- 重启mysql -->
    sudo systemctl restart mysqld.service
    <!-- 初始密码，MySQL第一次启动后会创建超级管理员账号root@localhost，初始密码存储在日志文件中 -->
    sudo grep 'temporary password' /var/log/mysqld.log
    <!-- 修改密码 -->
    ALTER USER 'root'@'localhost' IDENTIFIED BY 'jesonlin';
    <!-- 关闭验证密码复杂度策略 -->
    vim /etc/my.cnf
    <!-- 添加如下并重启 -->
    validate_password=OFF
    <!-- 运行远程访问 -->
    GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'jesonlin' WITH GRANT OPTION;
    <!-- 查看并修改字符集 -->
    vim /etc/my.cnf
    [mysqld]
    character-set-client-handshake=FALSE
    character_set_server=utf8mb4
    collation-server=utf8mb4_unicode_ci
    init-connect='SET NAMES utf8mb4'
    [client]
    default-character-set=utf8mb4
    [mysql]
    default-character-set=utf8mb4
    <!-- 重启并检查设置效果 -->
    SHOW VARIABLES WHERE Variable_name LIKE 'character_set_%' OR Variable_name LIKE 'collation%';
    <!-- 设置开机启动 -->
    systemctl enable mysqld
    systemctl daemon-reload
    ```
2. centos8安装mysql
    ```
    <!-- 前面下载yum源步骤同上，官方源里只有mysql8版本，需要手动修改源 -->
    <!-- 修改mysql-community.repo -->
    vim /etc/yum.repos.d/mysql-community.repo
    <!-- 添加如下 -->
    [mysql57-community]
    name=MySQL 5.7 Community Server
    baseurl=http://repo.mysql.com/yum/mysql-5.7-community/el/7/$basearch/
    enabled=1
    gpgcheck=0
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
    <!-- 禁止mysql8 -->
    yum-config-manager --disable mysql80-community
    <!-- 核对输出 -->
    yum repolist enabled | grep mysql
    <!-- 失能模块 -->
    yum module disable mysql
    <!-- 安装 -->
    yum install mysql-community-server
    <!-- 启动mysql -->
    systemctl start mysqld.service
    <!-- 其他步骤同上 -->
    <!-- 修改密码有报错执行下面 -->
    flush privileges;
    <!-- 重启失败，发现不能添加如下语句 -->
    validate_password=OFF
    ```
3. docker 安装启动mysql5.7
    ```
    docker pull mysql:5.7

    docker run -p 3307:3306 --name mysql -v /data/service/docker/mysql/logs:/var/log/mysql -v /data/service/docker/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=jesonlin -d mysql:5.7
    ```
4. 跳过密码登录数据库
    ```
    <!-- 设置MYSQLD_OPTS -->
    systemctl set-environment MYSQLD_OPTS="--skip-grant-tables";
    <!-- 显示mysql环境变量 -->
    systemctl show-environment
    <!-- 撤销mysql环境变量 -->
    systemctl unset-environment MYSQLD_OPTS;
    <!-- 重启，脚本里有用到该参数  -->
    systemctl restart mysqld.service 
    ```

## 权限
1. 创建用户
    ```
    CREATE USER '[用户名]'@'%' IDENTIFIED BY '[密码]';
    ```
2. 授予权限
    ```
    GRANT ALL PRIVILEGES ON `[DB]`.* TO '[用户名]'@'[host]';
    ```  
3. 删除用户
    ```
    drop user [用户名]@'%';
    ```
4. 删除权限
    ```
    revoke all on [DB].* from '[用户名]'@'[host]';
    Delete from mysql.user where user = '[用户名]' and host = '[host]';
    ```
5. 改密码
    ```
    mysqladmin -uroot -p[旧密码] password [新密码]
    set password for [用户名]@[host] = password([新密码]);
    update mysql.user set authentication_string=password('新密码') where user='用户名' and Host ='localhost';
    flush privileges;
    ``` 

## 库表操作
0. 启动、重启、停止
    ```
    systemctl start/restart/stop mysqld.service
    <!-- 查看启动状态 -->
    systemctl status mysqld.service
    ```
1. 创建数据库
    ```
    create database [DB] if not exists [DB] default charset utf8mb4 collate utf8mb4_unicode_ci;
    ```
2. 创建数据表
    ```
    CREATE TABLE `[表名]` (
      `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '自增ID',
      `name` varchar(32) NOT NULL COMMENT '名称',
      `comment` varchar(255) NOT NULL COMMENT '备注',
      `create_time` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
      `update_time` datetime DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
      PRIMARY KEY (`id`),
      KEY `idx_name` (`name`),
    ) ENGINE=InnoDB AUTO_INCREMENT=0 DEFAULT CHARSET=utf8mb4 COMMENT = '业务信息';
    ```
3. 插入数据
    ```
    insert into [表名] (id, name, comment) values (null, '', '');
    ```
4. 拷贝表结构
    ```
    create table [新表名] like [旧表名]
    ```
5. 拷贝数据
    ```
    insert into [新表名] select * from [旧表名]
    ```
6. 导入导出数据库
    ```
    mysqldump -h[IP] --set-gtid-purged=off -uroot -p[PASSWORD] --databases [DB] > [DB].sql
    mysql -h[IP] -uroot -p[PASSWORD] < [DB].sql
    ```
7. 数据库大小
    ```
    select (sum(DATA_LENGTH)+sum(INDEX_LENGTH))/1024/1024/1024 from information_schema.TABLES where TABLE_SCHEMA='[DB]';
    ```
8. 数据库每个表大小
    ```
    SELECT TABLE_NAME, DATA_LENGTH/1024/1024/1024, INDEX_LENGTH/1024/1024/1024 FROM information_schema.TABLES WHERE TABLE_SCHEMA='[DB]';
    ```
9. 数据表大小
    ```
    SELECT count(TABLE_NAME) FROM information_schema.TABLES WHERE TABLE_SCHEMA='[DB]';
    ```
10. 查询表创建和更新时间
    ```
    select * from information_schema.tables where information_schema.tables.TABLE_SCHEMA='[DB]' and information_schema.tables.TABLE_NAME='[TB]'\G;
    ```
11. 添加表字段
    ```
    alter table [TB] add [FIELD] int(11) unsigned default 0 comment "id";
    ```
12. 到出到本地csv
    ```
    mysql --host=[HOST] --user=[USER] --password=[PASSWORD] -se "SELECT * FROM [DB].[TB]" | sed 's/\t/","/g;s/^/"/;s/$/"/;s/\n//g' > app.csv
    ```


## 字段操作
1. 修改表字段类型：  
    ```
    alter table [TB] modify 字段名 varchar(20);
    ```
2. 增加表字段：  
    ```
    alter table [TB] add column age int(3);
    ```
3. 增加表字段时控制顺序（或者使用first）：  
    ```
    alter table [TB] add column age int(3) after [旧字段名];
    ```
4. 删除表字段：  
    ```
    alter table [TB] drop [字段名];
    ```
5. 修改表字段名：  
    ```
    alter table [TB] change [字段原名] [字段新名] [字段类型];
    ```


## 索引
1. 普通索引
    ```
    alter table 表 add idnex(字段);
    ```  
2. 唯一索引
    ```
    alter table 表 add unique(字段);
    ```
3. 全文索引
    ```
    alter table 表 add fulltext(字段);
    ```
4. 主键索引
    ```
    alter table 表 add primary key(字段);
    ```


## 查看任务及其状态
1. 查看任务及其状态 
    ```
    show full processlist;
    ```
2. 过滤掉Sleep链接
    ```
    pager grep -v Sleep
    ```
3. 结束单个任务
    ```
    kill Id
    ```
4. 批量结束任务
    ```
    select concat('kill ',id,';') from information_schema.processlist where info like '%INSERT INTO `log`%';
    ```
5. 查看和修改最大连接
    ```
    show variables like '%max_connections%';
    set GLOBAL max_connections = 2000;
    ```


## 常用语句
1. 配置变量查看和修改
    ```
    show variables like '%max_allowed_packet%';
    set global max_allowed_packet=1073741824;
    ```
2. 查看和修改引擎
    ```
    ALTER TABLE table_name ENGINE=InnoDB;
    select CONCAT('alter table ', table_schema, '.', table_name,' engine=InnoDB;') FROM information_schema.tables WHERE table_schema="interface_manage" AND ENGINE="MyISAM";    
    ```
3. 统计每天任务量
    ```
    select count(*),DATE_FORMAT(FROM_UNIXTIME(time), '%Y-%m-%d') AS t from [TB] WHERE DATE_FORMAT(FROM_UNIXTIME(time), '%Y') = '2020' GROUP BY t
    ```
4. 批量删除符合某些字符串的数据表
    ```
    <!-- 以下拼接结果有长度限制，可重复多次得到结果 -->
    SELECT CONCAT( 'DROP TABLE ', GROUP_CONCAT(table_name) , ';' ) AS statement FROM information_schema.tables WHERE table_schema = [DB] AND table_name LIKE '[TB]%';
    <!-- 将拼接得到的结果执行删除 -->
    ```

    
## REFERENCE
[廖雪峰SQL教程](https://www.liaoxuefeng.com/wiki/1177760294764384)  
[cenos安装mysql](https://juejin.cn/post/6844903870053761037)

