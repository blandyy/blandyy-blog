---
title: kafka 查看 topic 的偏移量
date: 2023-05-12 22:52:41
tags: Tools
---

```bash
# 1. 列举消费者
./kafka-consumer-groups.sh --bootstrap-server localhost:9092 --list

# 2. 查看偏移量
./kafka-consumer-groups.sh --bootstrap-server localhost:9092 --group consumer --describe

# 3. 移动至最新
./kafka-consumer-groups.sh --bootstrap-server localhost:9092 --group multi-alarms-pro --topic Alarms --reset-offsets --to-latest -execute
```

