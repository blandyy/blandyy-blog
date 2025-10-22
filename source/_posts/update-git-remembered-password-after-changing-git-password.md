---
title: 修改git密码后重置更新git记住的密码
date: 2023-06-07 17:06:46
categories: 操作系统
---
---
## 背景

域账号密码到期了，进行修改之后提交代码发现git默认记住的密码还是原来的，需要重新重置一下密码进行更新。

<!-- more -->

## 操作步骤

重置密码

Bash

```bash
git config --system --unset credential.helper
git config --global credential.helper store
```

