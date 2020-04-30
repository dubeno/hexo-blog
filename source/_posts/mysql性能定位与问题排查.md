---
title: mysql性能定位与问题排查
date: 2018-11-17 21:27:37
tags:
---

##### 1.线程相关计数，

MySQL是单进程多线程的数据库，每一个用户连接，是由一个对应的线程来服务的。

```
show global status like 'thread%';
```

当thread_created不断增加，表明存在短链接访问DB，

处理方式：

- 增大thread_cache_size


- 改短链为长链接

##### 2. table cache

在访问表之前，首先需要打开表，在代码实现上，就是获得这个表的TABLE对象，

打开表就是获取table对象，MySQL 5.7默认有16个table cache 实例 (表缓存池实例)

```
show global status like '%table%';
```

表缓存没有命中Table_open_cache_misses的数量。如果该值很大，你可能需要增加table_cache的数值

处理方式：

- 增大	table_open_cache	

mysql连接数

```
show status like 'Threads_connected';
```

多少线程正在跑

```
show status like 'Threads_running';
```

异常中断连接数

```
show status like 'Aborted_clients';
```

看下慢查询个数

```
show status like 'Slow_queries';
```

客户端连接进程数

```
show processlist;
```

innodb状态

```
show innodb status;
```

key Buffer命中率

```
key_buffer_read_hits = (1-key_reads / key_read_requests) * 100%
key_buffer_write_hits = (1-key_writes / key_write_requests) * 100%
show status like 'Key%';
```

InnoDB Buffer命中率

```
Innodb_buffer_read_hits = (1 - innodb_buffer_pool_reads / innodb_buffer_pool_read_requests) * 100%
show status like 'innodb_buffer_pool_read%';
```

Query Cache命中率

```
Query_cache_hits = (Qcahce_hits / (Qcache_hits + Qcache_inserts )) * 100%;
show status like 'Qcache%';
```

Table Cache状态量

```
show status like 'open%';
```

Thread Cache命中率

```
Thread_cache_hits = (1 - Threads_created / connections ) * 100%
show status like 'Thread%';
show status like 'Connections';
```

锁状态

```
 show status like '%lock%';
```

复制延时

```
show slave status
```

临时表

```
show status like 'Create_tmp%';
```

binlog cache

```
show status like 'Binlog_cache%';
```

Innodb_log_waits量

```
show status like 'innodb_log_waits';
```

数据库QPS:

```
Questions = SHOW GLOBAL STATUS LIKE 'Questions';
Uptime = SHOW GLOBAL STATUS LIKE 'Uptime';
QPS=Questions/Uptime
```

数据库TPS:

```
Com_commit = SHOW GLOBAL STATUS LIKE 'Com_commit';
Com_rollback = SHOW GLOBAL STATUS LIKE 'Com_rollback';
Uptime = SHOW GLOBAL STATUS LIKE 'Uptime';
TPS=(Com_commit + Com_rollback)/Uptime
```











