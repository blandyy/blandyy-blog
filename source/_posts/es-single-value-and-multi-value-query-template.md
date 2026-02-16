---
title: es单值与多值查询模板
date: 2023-05-09 19:00:14
tags: Tools
---
## 背景
在开发过程中，经常遇到要去es中查询数据排查问题的情况，这时候就需要有一个简单的模板带入参数进行查询。

## 语句
```bash
# 单值
curl -XGET  localhost:9200/xxx/_search?pretty -d '{"query":{"term":{"":""}},"from":0,"size":1}'
# 多值
curl -XGET  localhost:9200/xxx/_search?pretty -d '{"query":{"bool":{"must":[{"term":{"attacker":"x.x.x.x"}},{"term":{"victim":"x.x.x.x"}}]}},"from":0,"size":1}'
```
