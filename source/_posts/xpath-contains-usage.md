---
title: xpath中contains的用法
date: 2023-12-15 14:14:07
categories: 前端
---
在XPath中，contains() 函数用于在文本节点中检查是否包含指定的子字符串。它的语法如下：

```javascript
contains(string, substring)
```

<!-- more -->

其中：

string 是要搜索的字符串。

substring 是要查找的子字符串。

contains() 函数返回一个布尔值，如果字符串中包含子字符串，则返回 true；否则返回 false。

以下是一些使用 contains() 函数的示例：

1\. 匹配包含特定文本的节点：

```javascript
   //div[contains(text(), 'example')]
```

   这将匹配所有 <div> 元素，其文本节点包含 "example" 子字符串。

2\. 匹配包含特定属性值的节点：

```javascript
   //a[contains(@href, 'example')]
```

   这将匹配所有 <a> 元素，其 href 属性值包含 "example" 子字符串。

3\. 匹配包含特定类名的节点：

```javascript
   //*[contains(@class, 'example')]
```

   这将匹配所有具有包含 "example" 子字符串的类名的元素。

请注意，contains() 函数区分大小写。如果要进行不区分大小写的匹配，可以使用 lower-case() 或 upper-case() 函数转换字符串为统一的大小写，然后再使用 contains() 函数进行匹配。

例如，要进行不区分大小写的匹配，可以使用以下方式：

```javascript
//div[contains(lower-case(text()), 'example')]
```

这将匹配所有 <div> 元素，其文本节点包含不区分大小写的 "example" 子字符串。

希望这可以帮助你理解 contains() 函数在XPath中的用法。
