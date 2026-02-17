---
title: 在pg中终止一条执行中的语句
date: 2023-05-09 17:42:21
tags: Tools
---
## 背景
我们在使用分区表的时候经常会使用定时任务创建分区表，但是如果表链接没有完全断开，就会锁表。此时就需要一条语句将锁表的sql给终止掉。

## 语句
```bash
SELECT pg_terminate_backend(PID);
```
pid即为对应的sql语句的pid。

