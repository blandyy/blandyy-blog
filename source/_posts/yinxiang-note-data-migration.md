---
title: 印象笔记数据迁移
date: 2025-10-23 14:23:21
categories: Tools
---
## 背景
印象笔记我用了十多年了，层出不穷的会员等级和眼花缭乱的新功能，但却没有对其最核心的笔记功能做更好的优化，我决定将自己的笔记数据备份出来，并迁移到其他的笔记平台。

印象笔记7版本已经不支持导出enex格式的文档了，只能导出notes的格式，这个新格式是aes加密的，在其他的软件中无法读取，这就意味着你的数据要想迁移，只能在印象笔记内部流转，这也是促使我对他进行更换的初衷，我的数据一定是能便捷导出成可以解析的数据格式，不能被云笔记绑架。

<!-- more -->

## 迁移

### 印象笔记数据导出enex

印象笔记到版本7之后就不支持enex的导出了，我们需要通过一些特殊手段来导出enex。一是去寻找6版本的印象笔记客户端，然后登录账号导出，二是使用一些开源工具导出印象笔记数据为enex。
由于印象笔记旧客户端很难寻找，其他人分享的非官方可能存在安全问题，我直接使用开源工具evernote-backup导出。

#### 安装evernote-backup

github地址：https://github.com/vzhd1701/evernote-backup

从releases根据系统下载最新的版本：https://github.com/vzhd1701/evernote-backup/releases

我是Windows系统，下载https://github.com/vzhd1701/evernote-backup/releases/download/1.13.1/bin_evernote_backup_1.13.1_win_x64.zip

下载之后解压就可以得到执行文件 evernote-backup.exe

#### evernote-backup.exe使用

首先创建一个目录存放印象笔记导出数据`yinxiang_output`
将exe放到目录下

执行`evernote-backup init-db --backend china`进行初始化，如果是国际版的evernote，直接执行`evernote-backup init-db`。
按照提示登录账号即可。

输出：
```bash
evernote-backup init-db --backend china
Logging in to Evernote...
Using password authentication...
Username or Email: xxxxxxx
Password:
Authorizing auth token, china backend...
Successfully authenticated as xxxxxx!
Current login expires at 2035-10-21 06:49:49 (3649 days left).
Initializing database en_backup.db...
Reading database en_backup.db...
Successfully initialized database for xxxxxxx!
```

接着使用`evernote-backup sync`同步账号数据。

输出：
```bash
evernote-backup sync
Reading database en_backup.db...
Authorizing auth token, china backend...
Successfully authenticated as xxxxxxx!
Current login expires at 2035-10-21 06:59:45 (3649 days left).
Syncing user notebooks...
  [####################################]  20148/20148
237 note(s) to download...
Downloading 237 note(s)...
  [####################################]  237/237
Updated or added notebooks: 13
Updated or added notes: 237
Expunged notebooks: 26
Expunged notes: 1026
Synchronization completed!
```

使用`evernote-backup export output_dir/`命令导出笔记为enex

输出：
```bash
evernote-backup export output_dir/
Reading database en_backup.db...
Exporting notebooks...
  [####################################]  13/13
All notes have been exported!
```

在当前目录的output_dir目录下即可查看导出的笔记。

### 将enex转换为md

enex文件已经有了，可以将其导入到joplin和obsidian中了，如果想要在本地自己管理笔记，可以将其转换为markdown文件。

#### yarle下载

github: https://github.com/akosbalasko/yarle

从releases根据系统下载最新的版本：https://github.com/akosbalasko/yarle/releases

#### yarle使用

Windows上直接打开exe，会出现gui界面，选择要转换的enex，然后选择要转换到哪个平台的md格式，一路next即可。
