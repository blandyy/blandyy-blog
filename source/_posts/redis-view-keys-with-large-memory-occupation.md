---
title: redis查看占用内存大的key
date: 2023-05-15 10:35:41
categories: 数据库
---
## 背景

在测试环境中，经常出现某些同事乱用redis存数据，导致redis内存不够用，所以有下面的命令来查redis的内存占用和占用大的key。

<!-- more -->

## 命令

### 查看redis占用内存信息

docker环境：

```bash
docker exec -i redis redis-cli -h 127.0.0.1 -p 6379 -a <password> -n 1 info Memory
```

redis命令：

```bash
info Memory
```

### 查看redis占用内存大的key

docker环境：

```bash
docker exec -i redis redis-cli -h 127.0.0.1 -p 6379 -a <password> -n 1 --bigkeys
```

终端执行：

```bash
redis-cli -h 127.0.0.1 -p 6379 -a <password> -n 1 --bigkeys
```

