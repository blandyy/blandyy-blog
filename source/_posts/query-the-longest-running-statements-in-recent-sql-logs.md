---
title: 查询最近一段sql日志中执行时间最长的语句
date: 2023-05-09 09:36:51
tags: Tools
---
查询PostgreSQL日志中执行时间最长的SQL语句，可使用以下命令：
```bash
tail -n 10000 postgresql-2022-09-15_090253.log | grep duration | sort -k 7 -r -n | head -n 10
```

也可直接通过`grep`过滤后结合`sort -k 7 -r -n`进行排序查询。

**注意**：使用该方法前，需要提前打开PG日志的执行时间打印功能。

