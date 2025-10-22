---
title: 麒麟高级服务器操作系统V10安装docker
date: 2023-05-23 22:29:19
categories: 操作系统
---
## 背景

服务采用docker启动，适配国产化麒麟V10系统，需要安装docker

## 软件包

使用二进制的安装方式进行安装，docker版本为19.03.15

docker下载地址：[https://download.docker.com/linux/static/stable/x86\_64/](https://download.docker.com/linux/static/stable/x86_64/)

选择对应版本的tgz包即可。

<!-- more -->

## 安装步骤

### 解压安装包

```bash
tar -zxf docker-19.03.15.tgz’
\cp docker/* /usr/bin/
```

### 创建配置文件

```bash
vi /etc/systemd/system/containerd.service
```

内容为：

```bash
[Unit]
Description=containerd container runtime
Documentation=https://containerd.io
After=network.target local-fs.target
[Service]
ExecStartPre=-/sbin/modprobe overlay
ExecStart=/usr/bin/containerd
Type=notify
Delegate=yes
KillMode=process
Restart=always
RestartSec=5
LimitNPROC=infinity
LimitCORE=infinity
LimitNOFILE=1048576
TasksMax=infinity
OOMScoreAdjust=-999
[Install]
WantedBy=multi-user.target
```

```bash
vi /etc/systemd/system/docker.service
```

内容为：

```bash
[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target docker.socket firewalld.service containerd.service time-set.target
Wants=network-online.target containerd.service
Requires=docker.socket
[Service]
Type=notify
# the default is not to use systemd for cgroups because the delegate issues still
# exists and systemd currently does not support the cgroup feature set required
# for containers run by docker
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
ExecReload=/bin/kill -s HUP $MAINPID
TimeoutStartSec=0
RestartSec=2
Restart=always
# Note that StartLimit* options were moved from "Service" to "Unit" in systemd 229.
# Both the old, and new location are accepted by systemd 229 and up, so using the old location
# to make them work for either version of systemd.
StartLimitBurst=3
# Note that StartLimitInterval was renamed to StartLimitIntervalSec in systemd 230.
# Both the old, and new name are accepted by systemd 230 and up, so using the old name to make
# this option work for either version of systemd.
StartLimitInterval=60s
# Having non-zero Limit*s causes performance problems due to accounting overhead
# in the kernel. We recommend using cgroups to do container-local accounting.
LimitNOFILE=infinity
LimitNPROC=infinity
LimitCORE=infinity
# Comment TasksMax if your systemd version does not support it.
# Only systemd 226 and above support this option.
TasksMax=infinity
# set delegate yes so that systemd does not reset the cgroups of docker containers
Delegate=yes
# kill only the docker process, not all processes in the cgroup
KillMode=process
OOMScoreAdjust=-500
[Install]
WantedBy=multi-user.target
```

```bash
vi /etc/systemd/system/docker.socket
```

内容为：

```bash
[Unit]
Description=Docker Socket for the API
[Socket]
# If /var/run is not implemented as a symlink to /run, you may need to
# specify ListenStream=/var/run/docker.sock instead.
ListenStream=/run/docker.sock
SocketMode=0660
SocketUser=root
SocketGroup=docker
[Install]
WantedBy=sockets.target
```

### 修改权限

```bash
chmod 755 /etc/systemd/system/docker.service
chmod 755 /etc/systemd/system/docker.socket
chmod 755 /etc/systemd/system/containerd.service
```

### 启动服务

```bash
systemctl enable --now containerd.service
groupadd docker
systemctl enable --now docker.socket  && systemctl enable --now docker.service
systemctl start docker
```
