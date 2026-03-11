---
title: openclaw配置nginx实现外部ip访问
date: 2026-03-11 22:41:29
categories: AI
tags: openclaw
---

## 背景

openclaw启动后默认是只能本地127.0.0.1:18789，但是我想要将openclaw的地址暴露出来，局域网中其他的机器也能不通过ssh隧道来访问，最简单的就是nginx反向代理

<!-- more -->

## 步骤

### 生成ssl证书

openclaw为了安全，外部ip访问只允许https，所以要先生成ssl证书

```bash
# 1. 创建证书存放目录
sudo mkdir -p /etc/nginx/ssl

# 2. 生成自签SSL证书（有效期365天）
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/nginx/ssl/openclaw.key \
  -out /etc/nginx/ssl/openclaw.crt
```

### 修改nginx配置文件

我直接修改default，如果有需要可以配置其他文件

```
sudo vim /etc/nginx/sites-enabled/default
```

将原有内容删除，增加以下内容：

```
# 强制将HTTP（80）重定向到HTTPS（443）
server {
    listen 80;
    listen [::]:80;
    server_name _;
    # 所有HTTP请求重定向到HTTPS
    return 301 https://$host$request_uri;
}

# HTTPS服务器块（核心）
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name _;

    # SSL证书路径
    ssl_certificate /etc/nginx/ssl/openclaw.crt;
    ssl_certificate_key /etc/nginx/ssl/openclaw.key;

    # 优化SSL配置（可选，提升安全性）
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;
    ssl_session_cache shared:SSL:10m;

    # 跨域配置（保留）
    add_header Access-Control-Allow-Origin *;
    add_header Access-Control-Allow-Methods 'GET, POST, OPTIONS, PUT, DELETE';
    add_header Access-Control-Allow-Headers 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Authorization';

    # 反向代理配置（保留WS核心）
    location / {
        proxy_pass http://127.0.0.1:18789;

        # 基础代理头
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # WS关键配置
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";

        # 超时配置
        proxy_connect_timeout 7d;
        proxy_send_timeout 7d;
        proxy_read_timeout 7d;
    }
}
```

### 更新 openclaw 的 allowedOrigins

```bash
sudo vim .openclaw/openclaw.js
```

修改controlUi.allowedOrigins为 ["*"]允许所有 HTTPS 源

```
{
  "gateway": {
    "mode": "local",
    "auth": {
      "mode": "token",
      "token": ""
    },
    "controlUi": {
      "allowedOrigins": ["*"]  // 允许所有 HTTPS 源
    }
  }
}
```

### 重启服务

```bash
# 1. 检查Nginx配置语法
sudo nginx -t

# 2. 重载Nginx
sudo systemctl reload nginx

# 3. 手动重启openclaw进程/Docker容器
sudo systemctl restart openclaw-gateway.service
```

### 验证

在浏览器中访问https://你的服务器IP
自签证书会提示 “不安全”，点击 “高级”→“继续访问”