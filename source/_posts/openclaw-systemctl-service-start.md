---
title: openclaw配置systemctl服务管理
date: 2026-03-11 13:49:46
tags: openclaw
---

## 背景

在机器上安装openclaw之后，使用`openclaw gateway --port 18789 --verbose`启动openclaw网关

但是这是一个同步的服务，关闭终端服务就关闭了，使用系统自带的systemctl来管理openclaw

## 步骤

### 前提

安装好NODE JS和openclaw

### 创建service文件

```bash
sudo vim /etc/systemd/system/openclaw-gateway.service
```

在文件中输入以下配置：

```
[Unit]
# 服务描述
Description=OpenClaw Gateway Service
# 服务依赖：网络就绪后启动
After=network.target

[Service]
# 执行用户（建议使用非root用户，如ubuntu，需确保该用户有相关权限；若必须root则写User=root）
User=root
# 执行组
Group=root
# 核心启动命令
ExecStart=/root/.nvm/versions/node/v24.14.0/bin/node /root/.nvm/versions/node/v24.14.0/bin/openclaw gateway --port 18789 --verbose
# 重启策略：服务异常退出时自动重启
Restart=always
# 重启间隔（秒）
RestartSec=5
# 日志重定向：标准输出和错误输出写入/var目录的日志文件
StandardOutput=append:/var/log/openclaw-gateway.log
StandardError=append:/var/log/openclaw-gateway-error.log
# 进程退出时的清理策略
KillMode=process
# 超时时间
TimeoutSec=30

[Install]
# 开机自启的目标级别：多用户模式
WantedBy=multi-user.target
```

保存文件退出即可

### systemctl添加服务

#### 重载

```bash
sudo systemctl daemon-reload
```
#### 启动

```bash
sudo systemctl start openclaw-gateway.service
```

#### 开机自启

```bash
sudo systemctl enable openclaw-gateway.service
```

#### 查看状态

```bash
sudo systemctl status openclaw-gateway.service
```

若输出中显示active (running)，说明服务启动成功。