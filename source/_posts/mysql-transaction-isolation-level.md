---
title: MySQL中的事务隔离级别
date: 2019-3-12 18:00:00
categories: 
  - 技术
tags: 
  - MySQL
  - 数据库进阶
---

#### 简介
MySQL数据库中有四种事务隔离级别，用来解决脏读、不可重复读、幻读等读数据方面的问题

#### 读数据问题
- 脏读(Dirty Read)
- 不可重复读(Non Repeatable Read)
- 幻读(Phantom Read)
##### 脏读
B事务读取到了A事务还没有提交的数据修改
##### 不可重复读
B事务的两次读取数据间，A事务对数据进行了修改，导致B事务两次读取值不一致
##### 幻读
B事务的两次读取数据间，A事务对数据条数进行了修改，导致B事务两次读取条数不一致，是不可重复读的一种特殊情况

#### 四种事务隔离级别
- 读未提交数据(READ UNCOMMITTED)
- 读已提交数据(READ COMMITTED)
- 可重复读(REPEATABLE READ)
- 串行化(SERIALIZABLE)
##### 读未提交数据
A事务可以看到B事务没有提交的插入和更新操作
##### 读已提交数据
A事务可以看到B事务已经提交的插入和更新操作，无法看到未提交操作
##### 可重复读
A事务可以看到B事务提交的插入操作，但无法看到B事务提交的更新操作，也无法看到未提交操作
##### 串行化
当数据库系统使用SERIALIZABLE隔离级别时，一个事务在执行过程中完全看不到其他事务对数据库所做的更新。当两个事务同时操作数据库中相同数据时，如果第一个事务已经在访问该数据，第二个事务只能停下来等待，必须等到第一个事务结束后才能恢复运行。因此这两个事务实际上是串行化方式运行

#### 对比
```
+-----------+------+-----------+-----+
|           | 脏读 | 不可重复读 | 幻读 |
+-----------+------+-----------+-----+
|读未提交数据|  是  |    是     | 是  |
+-----------+------+-----------+-----+
|读已提交数据|  否  |    是     | 是  |
+-----------+------+-----------+-----+
|可重复读    |  否  |    否     | 是  |
+-----------+------+-----------+-----+
|串行化      |  否  |    否     | 否  |
+-----------+------+-----------+-----+
```

#### MySQL查看与设置事务隔离级别
```
查看全局:select @@global.tx_isolation
查看本会话:select tx_isolation
设置全局:set global transaction isolation level read committed
设置本会话:set session transaction isolation level read committed
JDBC连接参数:sessionVariables=tx_isolation='READ-COMMITTEd'
```

#### 参考文档
> [数据库的读现象浅析](http://www.hollischuang.com/archives/900)
> [Confluence fails to start and throws 'MySQL session isolation level 'REPEATABLE-READ' is no longer supported' error](https://confluence.atlassian.com/confkb/confluence-fails-to-start-and-throws-mysql-session-isolation-level-repeatable-read-is-no-longer-supported-error-241568536.html)