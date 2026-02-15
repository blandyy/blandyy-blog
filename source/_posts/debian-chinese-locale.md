---
title: debian系统配置中文支持
date: 2025-11-16 11:15:14
categories: 操作系统
---
## 背景

最近使用了slax和puppy，都是基于debian的linux发行版，就想着安装中文化和中文输入法支持
这里以slax为例子，全程都是终端下操作，没有依赖于发行版的特殊功能

<!-- more -->

##  中文支持配置

### 卸载原来的locales

```bash
apt purge locales
```

### 安装locales

```bash
apt install locales
```

### 设置zh\_CN.UTF-8

```bash
dpkg-reconfigure locales
```

在显示的语言选择界面中使用下方向键到最后使用空格选中`zh_CN.UTF-8`(前面带\*号即是被选中)，然后按下回车键。

然后执行

```bash
locale-gen
```

### 设置环境变量

```bash
vim /etc/environment
```

文件末尾增加：

```bash
LANG=zh_CN.UTF-8
LANGUAGE=zh_CN:en_US
```

```bash
vim /etc/profile
vim ~/.profile
vim ~/.bashrc
```

文件末尾增加：

```bash
export LANG=zh_CN.UTF-8
export LANGUAGE=zh_CN:en_US
```

### 安装中文字体

```bash
apt install fonts-wqy-zenhei fonts-wqy-microhei
```

注销用户重新登录，打开vim之类的软件，已经可以显示中文了

### 设置中文时区

查看当前时区：

```bash
timedatectl
```

设置时区为`Asia/Shanghai`：

```bash
timedatectl set-timezone Asia/Shanghai
```

ntp同步时间

```
ntpdate -u time.nist.gov
```

## 安装中文输入法

使用ibus输入法，安装过几次，fcitx总是有点问题，ibus比较稳定

```bash
apt install ibus ibus-gtk ibus-pinyin im-config
```

启动ibus：

```bash
ibus-daemon -drx
```

此时状态栏应该出现了一个输入法图标

### 配置输入法变量

```bash
vim /etc/profile
```

在最后添加：

```bash
export GTK_IM_MODULE=ibus
export QT_IM_MODULE=ibus
export XMODIFIERS=@im=ibus
```

注销用户重新登录

在输入法的配置中添加拼音输入法，然后打开文本编辑器测试应该可以输入中文了。
