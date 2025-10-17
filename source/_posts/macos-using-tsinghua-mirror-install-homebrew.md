---
title: macos使用清华源安装homebrew
date: 2025-01-24 15:37:24
categories: 操作系统
---
## 背景

最近换回macbook，重装系统后安装homebrew，因为国内环境，使用清华源方式来安装

<!-- more -->

## 步骤

### 安装CLT For Xcode

macOS 用户需额外要求安装 Command Line Tools (CLT) for Xcode

在命令行输入 

```bash
xcode-select --install
```

### 设置环境变量

```bash
export HOMEBREW_BREW_GIT_REMOTE="https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git"
export HOMEBREW_CORE_GIT_REMOTE="https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git"
export HOMEBREW_INSTALL_FROM_API=1
export HOMEBREW_API_DOMAIN="https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles/api"
export HOMEBREW_BOTTLE_DOMAIN="https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles"
```

设置python的pypi镜像源为清华源

### 安装 Homebrew

```bash
git clone --depth=1 https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/install.git brew-install
/bin/bash brew-install/install.sh
rm -rf brew-install
```

### 安装成功后需将 brew 程序的相关路径加入到环境变量

以下针对基于 Apple Silicon CPU 设备上的 macOS 系统（命令行运行 uname -m 应输出 arm64）上的 Homebrew：

```bash
test -r ~/.bash_profile && echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.bash_profile
test -r ~/.zprofile && echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
```

对基于 Intel CPU 设备上的 macOS 系统（命令行运行 uname -m 应输出 x86\_64）的用户可跳过本步

## 参考资料

https://mirror.tuna.tsinghua.edu.cn/help/homebrew/
