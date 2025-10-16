---
title: Ubuntu本地部署deepseek R1 使用docker
date: 2025-02-27 21:51:46
categories: AI
---
## 背景

之前使用了windows的wsl子系统来部署DeepSeek R1，这次使用docker部署一下

<!-- more -->

## 步骤

### 安装docker

官方文档：https://docs.docker.com/engine/install/ubuntu/#prerequisites

卸载所有跟docker有关的包

```bash
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

### 设置 Docker 的存储库

```bash
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

![](image_2025-10-16_21-55-10.png)

![](image_2025-10-16_21-56-00.png)

### 列出docker版本

```bash
apt-cache madison docker-ce | awk '{ print $3 }'
```

![](image_2025-10-16_21-56-47.png)

### 安装docker

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

  
![](image_2025-10-16_21-57-26.png)

### 使用vullm部署deepseek R1

新建一个文件夹，然后创建一个models目录

#### 下载模型

仓库https://huggingface.co/deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B/tree/main

```bash
apt-get install git-lfs
mkdir -p models
cd models/
git clone https://huggingface.co/deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B
```

#### docker-compose.yml文件  

```bash
version: '3'
services:
  vllm:
    container_name: vllm
    restart: no
    image: vllm/vllm-openai:v0.7.2
    ipc: host
    volumes:
      - ./models:/models
    command: ["--model", "/models/DeepSeek-R1-Distill-Qwen-1.5B",  "--served-model-name", "deepseek-r1-distill-qwen-1.5b",  "--gpu-memory-utilization", "0.90", "--max-model-len", "1024"]
    ports:
      - 8000:8000
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: all
              capabilities: [gpu]
```

#### 启动服务  

```bash
docker-compose up -d
```