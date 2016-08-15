---
title: Mysql事务及实现
date: 2016-08-15 18:07:59
tags: MySQL
categories: 数据库
---
MySQL默认事务隔离级别是可重复读（Repeatable read），可以使用如下命令来查看：  
    
    show variables like 'tx_isolation'
<!-- more -->
可以使用如下方式指定全局或者单个会话的事务隔离级：  

    在my.cnf的[mysqld]配置块中指定：
    transaction-isolation = {READ-UNCOMMITTED | READ-COMMITTED | REPEATABLE-READ | SERIALIZABLE}
    
    在会话中使用如下语句进行设置：
    SET [SESSION | GLOBAL] TRANSACTION ISOLATION LEVEL {READ UNCOMMITTED | READ COMMITTED | REPEATABLE READ | SERIALIZABLE}     //这里的名字中没有连字符
    
    使用如下语句查询全局和会话事务隔离级：
    SELECT @@global.tx_isolation;
    SELECT @@session.tx_isolation;
    SELECT @@tx_isolation;
    

#### 数据库并发操作时可能出现的问题

**`更新丢失(Lost Update)`** ： 如果两个事务同时对同一行数据进行操作，则可能两个事务都更新失败

**`脏读(Dirty Reads)`** ： 一个事务的更新操作还未提交，另外一个事务进行读取操作；如果第一个事务进行了回滚操作，则第二个事务读取到了中间数据（脏数据）

**`不可重复读(Non-repeatable Reads)`** ： 在一个事务中两次读取同一行数据，读取到的结果却不一样；这是因为有可能第一个事务再第一次读取这行数据后，另外一个事务对这行数据进行了更新

**`幻读(Phantom Reads)`** ： 第一个事务已经读取/更新过的行中被另外一个事务插入了一行数据

#### MySQL实现的隔离级

**`未提交读取(Read Uncommitted)`** ： 不允许更新丢失而允许另外三种情况；一个事务在对一行数据进行更新时对此行加共享锁，所以另外一个事务不能更新此行，但是可以读取此行，所以这种情况下就可能出现脏读；一个事务对一行数据进行读取时，不加锁，所以会有不可重复读的问题

**`提交读取(Read committed)`** ： 不允许更新丢失和脏读取，允许不可重复读和幻读；一个事务在对一行数据进行更新时会对此行加排它锁，直到事务提交时才释放，所以这种隔离级别不会出现脏读(读取到未提及的数据)；一个事务在对一行数据进行读取时加共享锁，读取完立刻释放，在这个事务中并不能保证读过的数据不被另外一个事务的更新操作修改，所以会出现不可重复读的问题

**`可重复读(Repeatable Reads)`** ： 不允许更新丢失、脏读取和不可重复读，但有可能会有幻读问题；一个事务在对一行数据进行更新时会对此行加排它锁，直到事务提交时才释放；一个事务在对一行数据进行读取时对此行加共享锁，直到事务提交时才释放，不可重复读的问题也被解决；但是不管在更新还是读取时都不能阻止另外一个事务插入一行数据，所以会出现幻读；但是后面就会看到MySQL通过MVCC(多版本并发控制)解决了幻读问题

**`序列化(Serializable)`** ： 上述四种问题都不会出现；事务开始前对整个表加锁，这种隔离级当然也失去了并发性

