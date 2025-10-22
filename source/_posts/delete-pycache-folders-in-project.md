---
title: 一键删除项目中的__pycache__文件夹
date: 2023-10-27 14:37:06
categories:
- 代码
- Python
---
## 背景

在编码过程中，运行测试代码之后总会生成一些pyc文件在pycache目录下面，虽然修改.gitignore可以保证提交中不含有这些文件，但是要想获取一个干净的目录的话还是删掉为好。这里就编写了一个脚本，用来删除一个目录下所有的\_\_pycache\_\_目录，包括子目录下的。

<!-- more -->

## 代码

```python
import os
import shutil
# 需要遍历的目录
root_dir = "F:\work\xxx"
# 遍历目录
for dirpath, dirnames, filenames in os.walk(root_dir):
    if "__pycache__" in dirnames:
        # 获取 __pycache__ 目录的全路径
        pycache_dir = os.path.join(dirpath, "__pycache__")
        # 删除目录
        shutil.rmtree(pycache_dir)
        print(f"Removed: {pycache_dir}")
```
