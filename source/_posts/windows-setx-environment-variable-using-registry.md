---
title: Windows下setx重复设置环境变量解决-使用注册表设置环境变量
date: 2024-10-10 16:53:08
categories: 操作系统
---
## 背景

我有一个应用，在安装时执行一个install.bat脚本，其中使用setx将应用目录设置到Path环境变量中，但是在真正执行的时候出现了以下报错：

```bash
警告：正在保存的数据被截断为1024个字符
WARNING: The data being saved is truncated to 1024 characters.
```

<!-- more -->

但是我的环境变量应该没有超过这些字符才对，遂查看系统环境变量，发现setx将我的环境变量又重新设置了一遍

## 解决方案

使用reg即注册表设置环境变量

代码如下，包括判断是否环境变量中存在此目录，我使用的目录是用\\\\分割

```bash
chcp 65001 > nul
echo  把cmd设置成UTF-8
set "CURRENT_DIR=%cd:\=\\%"
echo 启动目录:%CURRENT_DIR%
rem 获取上一级目录
for %%i in ("%CURRENT_DIR%") do set "ROOT_DIR=%%~dpi"
rem 删除最后一个反斜杠
set "ROOT_DIR=%ROOT_DIR:~0,-1%"
echo ROOT_DIR: %ROOT_DIR%
rem 设置要添加的路径
set new_path=%ROOT_DIR:\=\\%
rem 用户变量所在位置：`HKEY_CURRENT_USER\Environment`
set USERregpath=HKEY_CURRENT_USER\Environment
rem 查询原有 PATH 的值
for /F "tokens=3*" %%i in ('reg query "%USERregpath%" /v "path" 2^>nul') do echo %%i %%j
for /F "tokens=3*" %%i in ('reg query "%USERregpath%" /v "path" 2^>nul') do ( set USERpath=%%i %%j)
echo USERpath=%USERpath%
rem 将new_path中的双反斜杠替换为单反斜杠
set new_path_single=%new_path:\\=\%
rem 将USERpath中的双反斜杠替换为单反斜杠
set USERpath_single=%USERpath:\\=\%
rem 检查PATH中是否包含new_path，不存在插入
echo %USERpath_single% | findstr /I /C:"%new_path_single%" > nul
if %errorlevel% equ 0 (
    echo %new_path% exist.
) else (
    reg add "%USERregpath%" /v "Path" /t REG_EXPAND_SZ /d "%new_path%;%USERpath%" /f
)
```

执行完脚本之后重启电脑或者注销重新登录即可
