---
title: docker根据overlay2的目录名找到对应的容器
date: 2023-06-06 22:24:11
categories: 操作系统
---
## 背景

在工作中，有一段旧代码在容器中使用了临时文件库tmpfile，并设置为不自动删除，所以临时文件一直堆积在/tmp目录下。

而容器中的tmp目录是不会清理的，终于有一天，整个var目录满了。

查到overlay2目录下的某个目录占用比较大，就需要根据目录名找到对应的容器

<!-- more -->

## 操作语句

```bash
docker ps -q | xargs docker inspect --format '{{.State.Pid}}, {{.Id}}, {{.Name}}, {{.GraphDriver.Data.WorkDir}}' | grep '目录名'
```
