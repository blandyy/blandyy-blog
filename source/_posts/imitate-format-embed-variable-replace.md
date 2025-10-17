---
title: 仿format()将变量嵌入替换到字符串中
date: 2024-07-18 17:36:59
categories:
- 代码
- Python
---
在工作中遇到一个需求，需要将一个变量数组按照顺序嵌入到字符串中。

这种需求第一想到的是强大的format函数，但format函数格式固定，需要使用{}，所以不满足需求，只好自己写一个替换的。

<!-- more -->

代码如下：

```python
def sting_embed(word: str, params: list) -> str:
    """将变量嵌入到字符串中"""
    if not params:
        return word
    # 判断字符串是否为变量嵌入
    if "${1}" not in word:
        return word
    # 约定使用${x}形式进行替换，x从1开始
    for param_position in range(1, len(params) + 1):
        word = word.replace(
            f"${{{str(param_position)}}}", params[param_position - 1]
        )
    return word
    
    
word = "汤姆说：${1}，吉米回答：${2}"
vars = ["你好", "我很好"]
print(sting_embed(word, vars))
```

其中按照列表的变量顺序替换字符串中的占位符，如果需要改成按照变量名替换，传入的是字典的代码如下：  

```python
def sting_embed(word: str, params: dict) -> str:
    """将变量嵌入到字符串中"""
    if not params:
        return word
    # 约定使用${x}形式进行替换，x为变量名
    for k, v in params.items():
        word = word.replace(
            f"${{{str(k)}}}", v
        )
    return word
    
    
word = "汤姆说：${one}，吉米回答：${two}"
vars = {"one":"你好", "two":"我很好"}
print(sting_embed(word, vars))
```
