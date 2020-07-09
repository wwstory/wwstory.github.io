---
title: '[InnoDB 学习1-5] Master Thread'
date: 2020-06-24 12:20:00
tags:
    - 'mysql'
    - 'innodb'
---


# Master Thread

具有最高的线程优先级别。

由多个循环组成：（Master Thread会根据数据库运行状态在其中切换）

- 主循环（loop）
- 后台循环（backgroud loop）
- 刷新循环（flush loop）
- 暂停循环（suspend loop）

## 主循环（loop）

主要是2大部分操作：`每秒的操作`、`每10秒的操作`。

> 通过`thread sleep`实现，时间并不精确，负载大可能会延迟。

每秒的操作：

- 日志缓冲刷新到磁盘，即使这个事务还没有提交。（总是）
- 合并插入缓冲。（可能，IO压力小时）
- 至多刷新100个InnoDB的缓冲池中的脏页到磁盘。（可能，脏页比率大时）
- 如果没有用户活动，则切换到background loop。（可能）

> 注：总是将日志缓冲的内容刷新到重做日志，这可以解释为什么再大的事务提交（commit）时间也是很短的。

每10秒操作：

- 刷新100个脏页到磁盘。（可能，IO压力小时）
- 合并至多5个插入缓冲。（总是）
- 将日志缓冲刷新到磁盘。（总是）
- 删除无用的Undo页。（总是）
- 刷新100个/10个脏页到磁盘。（总是）


`[伪代码]`
```c++
/* 1s */
for (int i=0; i<10; i++){
    thread_sleep(1);
    // do log buffer flush to disk
    if (last_one_second_ios < 5)
        // do merge at most 5 insert buffer
    if (buf_get_modified_ratio_pct > innodb_max_dirty_pages_pct)
        // do buffer pool flush 100 dirty page
    if (no_user_activity)
        goto backgroup_loop;
}

/* 10s */
if (last_ten_sencond_ios < 200)
    // do buffer pool flush 100 dirty page
// do merge at most 5 insert buffer
// do log buffer flush to disk
// do full purge
if (buf_get_modified_ratio_pct > 70%)
    // do buffer pool flush 100 dirty page
else
    // buffer pool flush 10 dirty page
```

> 注：新版本改善了硬编码的问题，改用百分比控制。

## 后台循环（backgroud loop）

当没有用户活动，或数据库关闭时，就会切换到这个循环。

操作：

- 删除无用的Undo页。（总是）
- 合并20个插入缓冲。（总是）
- 跳回主循环。（总是）
- 不断刷新100个页直到符合条件。（可能，跳转到flush loop中完成）

> 若`flush loop`中没事做，就会切换至`suspend loop`，将Master thread挂起，等待事件的发生。

---
