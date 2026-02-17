---
title: Docker 与防火墙问题 —— 不让 docker 创建 iptables 规则
date: 2023-04-28 17:53:26
tags: 操作系统
---
## 1. 背景
在某一天，线上被扫出来一个服务漏洞，但是那个服务漏洞的端口不在firewalld防火墙上开放，查了大半天，发现docker自动会在iptable上面添加规则。
默认情况下当Docker启动容器映射端口时，会直接使用iptables开启添加端口。
firewalld也是使用iptables对底层进行管理实现防火墙功能。所以就会出现防火墙没有开放端口，但容器启动后会自己开放，且在firewalld上是没有记录。

## 2. 测试问题
```bash
# 创建一个容器
docker run --name mysql -p 3306:3306 -d mysql
# 使用iptables命令进行查看
iptables -nL DOCKER
```
```
[ root@localhost ~]# iptables -nL
DOCKER
Chain DOCKER (4 references) target prot opt source destination
ACCEPT tcp -- 0.0.0.0/0 172.18.0.2 tcp dpt:3306
```

```bash
# 关闭容器
docker stop mysql
# 再查看防火墙规则
iptables -nL DOCKER
```
```
[ root@localhost spug]# iptables -nL DOCKER
Chain DOCKER (4 references) target prot opt source destination
```

这里可以看出是docker的问题，所以需要修改docker的配置。

## 3. 具体操作
### 关闭SELinux(非必须)
有的时候创建容器会因为SELinux的存在，导致一些奇奇怪怪的报错，所以这里先进行关闭。
```bash
getenforce # 查看SELinux状态
setenforce 0 # 临时关闭SELinux
vim /etc/selinux/config
# 把SELINUX=enforcing 改成 SELINUX=disabled
```
修改配置后重启linux就会生效。

### 调整Docker服务配置
```bash
# 先停止docker
systemctl stop docker
# 修改配置
vim /etc/docker/daemon.json
```
```
[ root@localhost spug]# cat /etc/docker/daemon.json
{"iptables":false}
```
```bash
# 重启docker
systemctl restart docker
```
