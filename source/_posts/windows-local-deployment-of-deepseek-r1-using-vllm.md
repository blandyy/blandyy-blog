---
title: Windows本地部署deepseek R1 使用Vllm
date: 2025-02-24 20:09:46
categories: AI
---
## 背景

最近DeepSeek比较火，准备本地部署一个用来做一个基础的RAG服务

## 环境

Python 3.12

WSL2 ubuntu22.04LTS

<!-- more -->

## 步骤

### 安装ubuntu子系统

在windows应用商店搜索ubuntu安装对应版本后设置好用户名密码即可。

### 让主机的代理适用于wsl2

Wsl2默认nat网络，本地代理不支持NAT，那么就改成其他模式就好了，具体的步骤如下

在你windows的用户文件夹下找到一个.wslconfig的文件，如果没有那你就自己创建一个，用户文件夹的路径通常是C:\\Users\\<这里是你自己的用户名>

在这个文件夹中输入以下的内容,networkingMode就是网络模式，默认是NAT，这里我们改成mirrored,镜像模式就是与windows本机的网络配置一样

```bash
[wsl2] 
networkingMode=mirrored
autoProxy=true
```

重启wsl

```bash
wsl --shutdown
wsl
```

> 官方文档：https://learn.microsoft.com/en-us/windows/wsl/networking

sudo 之后要设置一下本地代理

```bash
export http_proxy="http://127.0.0.1:1080"
export https_proxy="http://127.0.0.1:1080"
```

### 安装conda

我使用的是miniconda

```bash
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
```

使用代理的下载速度

 ![](image_2025-10-16_20-25-07.png)

```bash
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm ~/miniconda3/miniconda.sh
source ~/miniconda3/bin/activate
conda init --all
```

### 创建python3.12环境

```bash
conda create -n deepseek python=3.12 -y
```

进入环境

```bash
conda activate deepseek
```

### 按照官方文档安装cuda

```bash
https://learn.microsoft.com/zh-cn/windows/ai/directml/gpu-cuda-in-wsl
```

#### 按照自己的电脑配置下载对应驱动

![](image_2025-10-16_20-38-07.png)

![](image_2025-10-16_20-38-42.png)

![](image_2025-10-16_20-40-58.png)

#### 按照说明安装cuda toolkit

![](image_2025-10-16_20-41-44.png)

https://developer.nvidia.com/cuda-downloads?target\_os=Linux&target\_arch=x86\_64&Distribution=WSL-Ubuntu&target\_version=2.0

![](image_2025-10-16_20-42-18.png)

#### 选择对应版本

可以选择deb的network安装方式，因为我们后面使用vllm，最好安装cuda12.1

![](image_2025-10-16_20-43-11.png)

在最后一步安装执行前可以先搜索一下cuda-toolkit的版本

```bash
apt-cache search cuda-toolkit
```

![](image_2025-10-16_20-43-34.png)

根据vllm的要求，我们安装12.1

![](image_2025-10-16_20-43-57.png)

执行命令安装

```bash
apt-get -y install cuda-toolkit-12-1
```

安装完成如图所示

![](image_2025-10-16_20-44-21.png)

#### 设置环境变量

```bash
vim ~/.bashrc
```

文件末追加

```bash
export PATH=/usr/local/cuda-12.1/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-12.1/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
export PATH="/usr/lib/wsl/lib/:$PATH"
```

配置生效

```bash
source ~/.bashrc
```

然后关闭终端窗口，执行

```bash
wsl --shutdown
wsl
```

#### 查看版本

![](image_2025-10-16_20-44-47.png)

### 安装vllm

```bash
python -m pip install vllm --extra-index-url https://download.pytorch.org/whl/cu12
```

### 下载模型

仓库： https://huggingface.co/deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B/tree/main

```bash
apt-get install git-lfs
mkdir -p models
cd models/
git clone https://huggingface.co/deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B
```

![](image_2025-10-16_20-45-18.png)

### 运行模型

```bash
vllm serve deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B --tensor-parallel-size 1 --max-model-len 32768 --enforce-eager
```