---
title: '[problem] pymongo 千万级数据查询缓慢的优化'
date: 2022-03-03 17:00:00
categories: 'problem'
tags:
    - 'problem'
---

# 问题

项目使用pymongo，某个表存放了2千万条数据，会指定一个xxx_id/xxx_name/xxx_number作为查询条件筛选数据，数据量大会分页。

由于数据量大，查询偏后的数据会长达6-7s的耗时。

# 解决

**分页问题：**

分析mongo的数据存放原理，采用B+树管理。由于mongo数据长度不定，无法像mysql那样容易skip做分页，会遍历，导致速度极慢。

**查询缓慢：**

采用传统做法，建索引，为xxx_name/xxx_number建立索引，查询得到飞速提升，毫秒内完成。

但结合业务，xxx_id字段，光xxx_id=1就有千万条，如果使用此作查询条件，查询速度还是回到解放前。考虑分表解决，以xxx_id名称作为表名： xxx_table-xxx_id。查询前，如果存在xxx_id条件，就可以先组合成新的子表名去查询；否则遍历所有子表。由于业务问题xxx_id不过百条，以此分表查询速度还可以。
