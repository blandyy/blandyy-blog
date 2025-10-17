---
title: werkzeug.security的generate_password_hash方法的使用
date: 2024-01-05 01:55:58
categories:
- 代码
- Python
---
## 背景

用户登录，使用简单安全的python第三方库，可以自定义加密方式和salt的长度。

## 使用

werkzeug.security是Python的一个库，提供了一些密码相关的功能，包括密码哈希和验证。其中，generate\_password\_hash()方法用于生成密码的哈希值。

下面是使用generate\_password\_hash()方法生成密码哈希的示例：

<!-- more -->

```python
from werkzeug.security import generate_password_hash
password = "my_password"
hash_value = generate_password_hash(password)
print(hash_value)
```

我们导入了generate\_password\_hash函数，并将明文密码存储在password变量中。然后，我们调用generate\_password\_hash()方法，将密码作为参数传递给它。该方法将返回密码的哈希值，将其存储在hash\_value变量中。最后，我们打印出哈希值。generate\_password\_hash()方法使用安全的密码哈希算法（如PBKDF2或bcrypt）来保护密码，并自动生成一个随机的盐值。这种哈希算法具有较高的安全性，可以防止彩虹表攻击和暴力破解。

## 扩展参数

如果您需要自定义哈希算法的参数（如迭代次数、盐长度等），generate\_password\_hash()方法还提供了一些可选参数。

```python
password（必需）: 要进行哈希处理的原始密码字符串。
method（可选）: 哈希算法的名称或标识符。默认为"pbkdf2:sha256"（PBKDF2哈希算法，使用SHA-256散列函数）。您可以使用其他支持的算法，如"bcrypt"或"sha512"。支持的哈希算法取决于您的Werkzeug版本。
salt_length（可选）: 盐值的长度（以字节为单位）。默认为16。
```
