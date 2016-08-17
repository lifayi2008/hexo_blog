---
title: MySQL日志及日志处理
date: 2016-08-17 17:13:00
tags: MySQL
categories: 数据库
---
查看日志相关系统变量
<!-- more -->
```
mysql> show variables like '%log%';
+-----------------------------------------+-------------------------------------+
| Variable_name                           | Value                               |
+-----------------------------------------+-------------------------------------+
| back_log                                | 80                                  |
| binlog_cache_size                       | 32768                               |
| binlog_checksum                         | CRC32                               |
| binlog_direct_non_transactional_updates | OFF                                 |
| binlog_error_action                     | IGNORE_ERROR                        |
| binlog_format                           | STATEMENT                           |
| binlog_gtid_simple_recovery             | OFF                                 |
| binlog_max_flush_queue_time             | 0                                   |
| binlog_order_commits                    | ON                                  |
| binlog_row_image                        | FULL                                |
| binlog_rows_query_log_events            | OFF                                 |
| binlog_stmt_cache_size                  | 32768                               |
| binlogging_impossible_mode              | IGNORE_ERROR                        |
| expire_logs_days                        | 0                                   |
| general_log                             | ON                                  |
| general_log_file                        | /usr/local/mysql/data/test.log      |
| innodb_api_enable_binlog                | OFF                                 |
| innodb_flush_log_at_timeout             | 1                                   |
| innodb_flush_log_at_trx_commit          | 1                                   |
| innodb_locks_unsafe_for_binlog          | OFF                                 |
| innodb_log_buffer_size                  | 8388608                             |
| innodb_log_compressed_pages             | ON                                  |
| innodb_log_file_size                    | 50331648                            |
| innodb_log_files_in_group               | 2                                   |
| innodb_log_group_home_dir               | ./                                  |
| innodb_mirrored_log_groups              | 1                                   |
| innodb_online_alter_log_max_size        | 134217728                           |
| innodb_undo_logs                        | 128                                 |
| log_bin                                 | OFF                                 |
| log_bin_basename                        |                                     |
| log_bin_index                           |                                     |
| log_bin_trust_function_creators         | OFF                                 |
| log_bin_use_v1_row_events               | OFF                                 |
| log_error                               | /usr/local/mysql/data/test.err      |
| log_output                              | FILE                                |
| log_queries_not_using_indexes           | OFF                                 |
| log_slave_updates                       | OFF                                 |
| log_slow_admin_statements               | OFF                                 |
| log_slow_slave_statements               | OFF                                 |
| log_throttle_queries_not_using_indexes  | 0                                   |
| log_warnings                            | 1                                   |
| max_binlog_cache_size                   | 18446744073709547520                |
| max_binlog_size                         | 1073741824                          |
| max_binlog_stmt_cache_size              | 18446744073709547520                |
| max_relay_log_size                      | 0                                   |
| relay_log                               |                                     |
| relay_log_basename                      |                                     |
| relay_log_index                         |                                     |
| relay_log_info_file                     | relay-log.info                      |
| relay_log_info_repository               | FILE                                |
| relay_log_purge                         | ON                                  |
| relay_log_recovery                      | OFF                                 |
| relay_log_space_limit                   | 0                                   |
| simplified_binlog_gtid_recovery         | OFF                                 |
| slow_query_log                          | OFF                                 |
| slow_query_log_file                     | /usr/local/mysql/data/test-slow.log |
| sql_log_bin                             | ON                                  |
| sql_log_off                             | OFF                                 |
| sync_binlog                             | 0                                   |
| sync_relay_log                          | 10000                               |
| sync_relay_log_info                     | 10000                               |
+-----------------------------------------+-------------------------------------+
61 rows in set (0.00 sec)

log_output 指定了常规日志和慢查询日志输出的目的地，可以设置为FILE和TABLE

general_log 和 general_log_file 分别指定了是否开启常规查询日志和日志输出文件位置

slow_query_log 和 slow_query_log_file 分别指定了是否开启慢查询日志和日志输出文件位置

log_error 指定了错误日志输出文件位置

```

#### 基本概述

根据设置MySQL会维护如下几种日志：

**`错误日志(error log)`** ： 这个日志记录服务器的启动和关闭情况，以及MySQL服务内部错误和异常情况；如果MySQL服务无法启动或者崩溃应该查看这个日志中的内容

    如果使用mysqld_safe启动Mysql的话会默认使用 --log-error=/mysql_home/data/hostname.err 参数来指定日志位置
    
    还可以在配置文件中增加 log-error=file 来指定其他的位置

**`常规查询日志(general log)`** ： 这个日志主要记录客户端连接、查询和各种其他操作的记录

    常规查询日志默认关闭，可以在配置文件中增加 general-log=ON 和 general_log_file=file 来开启并指定文件位置

**`慢查询日志(slow query log)`** ： MySQL服务器内部会维护一个名为`long_query_time`的变量（默认10秒），如果一个查询花费的时间超过这个变量指定的值，服务器会认为这是一个慢查询

    慢查询日志默认关闭，可以在配置文件中增加 slow-query-log=ON 和 slow_query_log_file=file 来开启并指定文件位置
    
    可以使用 long_query_time=n 来指定慢查询的时间
    可以使用 min_examined_row_limit=n 来指定要记录的慢查询操作至少要扫描的行数


**`二进制日志(binary log)和二进制日志索引文件`** ： 一个或者多个二进制格式的日志文件，记录了对服务器进行更改操作的查询语句（UPDATE、DELTE、INSERT、CREATE TABLE、DROP TABLE、GRANT等）；二进制日志文件需要有一个对应的索引文件，保存了服务器现有的二进制日志文件和当前读写的日志文件
    
    二进制日志有两个基本用途：
    
        1. 可以配合备份文件在系统崩溃时对数据进行恢复
        2. 数据库复制时将二进制日志发送到从服务器执行

    可以使用 log-bin = [basename] 来开启二进制日志并且指定二进制日志文件的前缀，如果不指定basename则使用hostname-bin作为前缀
    可以使用 log-bin-index = basename[.suffix] 指定索引文件名，如果不使用这个选项则默认使用hostname.index作为索引文件名
    可以使用 binlog-format = [STATEMENT|ROW|MIXED] 选项指定二进制日志记录方式，默认STATEMENT表示直接记录操作语句，ROW表示记录影响的行的内容，MIXED表示以记录数据行为主，在必要的时候记录操作语句
    
    还有一些更详细的二进制日志相关的参数可以参考<<MySQL配置文件参数详解>>一篇
    
**`中继日志(relay log)和中继日志索引文件`** ： 这个日志存在于主从复制的从服务器上，主要记录了从主服务器接收到的还未执行的操作记录，格式和二进制日志文件一样，并且也有一个索引文件记录了服务器上现有的中继日志文件
    
    中继日志的启用方法和命名同二进制日志


#### 日志轮替

MySQL服务器内部实现了对二进制日志进行轮替的方法；在服务器重新启动或者`flush-logs`命令或者日志文件大小达到`max_binlog_size`指定的值时服务器会进行日志轮替；`expire_logs_days`选项参数指定了二进制日志保存的天数

使用`mysqladmin flush-logs -u log -plogpassword`外部命令或者使用`FLUSH LOGS`查询命令可以刷新日志，这个对除error log之外的其他三种日志都起作用，所以我们可以创建一个自定义脚本来对日志文件进行轮替

    创建一个刷新日志的用户：
    GRANT RELOAD ON *.* TO 'log'@'localhost' IDENTIFIED BY 'log';  

```bash
#!/bin/sh  
# log refresh   
  
if [ ! -f $1 ]; then  
    echo "log file do not exist;"  
    exit 1  
fi  
  
LOG=$1  
DB_USER="log"  
DB_PASS="log"                                                               
BIN_DIR="/usr/local/mysql/bin"
  
# Others vars  
DATE=`date +%w`                                          
  
mv ${LOG} ${LOG}_${DATE}  
  
${BIN_DIR}/mysqladmin  -ulog -plog flush-logs 
```
可以将上面的脚本文件加入crontab计划任务对常规查询日志和慢日志进行轮替
