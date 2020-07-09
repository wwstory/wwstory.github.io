---
title: '[InnoDB 学习1-4] checkpoint'
date: 2020-06-24 12:20:00
tags:
    - 'mysql'
    - 'innodb'
---


# Checkpoint技术

> 缓冲池的设计目的是为了协调CPU速度与磁盘速度的鸿沟。因此页的操作首先都是在缓冲池中完成的。如果一条DML语句，如Update/Delete改变了页中的记录，那么此时页是脏的，即缓冲池中的页版本要比磁盘的新。数据库需要将新版本的页从缓冲池刷新到磁盘。

若每次一个页发生变化，就将新页刷新到磁盘，那么这个开销是非常大的。

同时，如果在从缓冲池将页的新版本刷新到磁盘时发生了宕机，那么数据就不能恢复了。

为了避免发生数据丢失的问题，当前事务数据库系统普遍采用了`Write Ahead Log`策略，即当事务提交时，先写重做日志，再修改页。当由于发生宕机而导致数据丢失时，通过重做日志来完成数据的恢复。（这也是事务ACID中D(Durability持久性)的要求）

## 为什么需要Checkpoint技术？

重做日志无限增大，可以还原整个数据库，但成本高，不便运维。

因此Checkpoint技术的目的是解决以下几个问题：

- 缩短数据库的恢复时间。
- 缓冲池不够用时，将脏页刷新到磁盘。
- 重做日志不可用时，刷新脏页。

当数据库发生宕机时，数据库不需要重做所有的日志，因为Checkpoint之前的页都已经刷新回磁盘。故只需要对Checkpoint后的重做日志进行恢复。

当缓冲池不够用时，根据LRU算法会溢出最近最少使用的页，若此页为脏页，那么需要强制执行Checkpoint，将脏页的新版本刷回磁盘。

重做日志出现不可用的情况是因为当前事务数据库系统对重做日志的设计是循环使用的。若重做日志需要使用，那么必须强制产生Checkpoint，**将缓冲池中的页至少刷新到当前重做日志的位置。**

> 对于InnoDB存储引擎而言，其是通过LSN（Log Sequence Number）来标记版本的。LSN是8字节的数字，每个页有LSN，重做日志中也有LSN，Checkpoint也有LSN。
> ```sh
> mysql> show engine innodb status \G;
> ---
> LOG
> ---
> Log sequence number 2660223
> Log flushed up to   2660223
> Pages flushed up to 2660223
> Last checkpoint at  2660214
> ```

## 何时触发？

Checkpoint发生的时间、条件及脏页的选择等都非常复杂。

而Checkpoint所做的事情无外乎是将缓冲池中的脏页刷回磁盘。不同之出在于每次刷新多少页到磁盘，每次从哪里取脏页，以及什么时间触发Checkpoint。

在InnoDB存储引擎内部，有2种Checkpoint，分别是：

- Sharp Checkpoint
- Fuzzy Checkpoint

### Sharp Checkpoint

发生在数据库关闭时，将所有的脏页都刷新回磁盘。（默认）

### Fuzzy Checkpoint

若数据库在运行时也使用Sharp Checkpoint，那么数据库的可用性就会受到很大的影响。

故在InnoDB存储引擎内部使用Fuzzy Checkpoint进行页的刷新，即只刷新一部分脏页，而不是刷新所有的脏页回磁盘。

以下是集中可能发生的Fuzzy Checkpoint：

- `Master Thread Checkpoint`：差不多以每秒/每十几秒的素多从缓冲池的脏页列表中刷新一定比例的页回磁盘。（`异步`）
- `FLUSH_LRU_LIST Checkpoint`：是因为InnoDB存储引擎需要保证LRU列表中需要有差不多100个空闲页可供使用。若没有100个可用空闲页，那么InnoDB存储引擎会将LRU列表尾端的页移除。如果这些页中有脏页，那么需要进行Checkpoint。（单独的`Page Cleaner线程`中进行）
```sh
mysql> show variables like 'innodb_lru_scan_depth';
+-----------------------+-------+
| Variable_name         | Value |
+-----------------------+-------+
| innodb_lru_scan_depth | 1024  |
+-----------------------+-------+
```
- `Async/Sync Flush Checkpoint`：重做日志不可用时，需要强制将一些页刷新回磁盘。（保证重做日志的循环使用的可用性。）（单独的`Page Cleaner线程`中进行）
- `Dirty Page too much Checkpoint`：脏页数量太多，必须强制进行Checkpoint。（主要目的是为了保证缓冲池中有足够可用的页。）
```sh
mysql> show variables like 'innodb_max_dirty_pages_pct';
+----------------------------+-----------+
| Variable_name              | Value     |
+----------------------------+-----------+
| innodb_max_dirty_pages_pct | 75.000000 |
+----------------------------+-----------+
```
