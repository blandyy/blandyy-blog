---
title: centos7安装python3
date: 2023-12-15 14:04:12
categories: 操作系统
---
## 背景

在服务器上部署服务，发现centos7的服务器没有python3，故而部署python3.9。

## 部署步骤

## 1.下载python安装包

wget后面的python安装包可以换版本，安装步骤不变。

```bash
wget https://www.python.org/ftp/python/3.9.16/Python-3.9.16.tgz
```

<!-- more -->

## 2.替换centos7软件源为阿里源

```bash
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
yum clean all
yum makecache
```

## 3.安装编译python3的依赖

```bash
yum groupinstall "Development Tools" -y
yum install openssl-devel libffi-devel bzip2-devel -y
```

## 4.解压

```bash
tar -xf Python-3.9.16.tgz
cd Python-3.9.16/
```

## 5.解压安装

```bash
./configure --enable-optimizations
make
make install
```
