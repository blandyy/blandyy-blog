---
title: Xpath选择元素名称相等和包含特定字符串的元素
date: 2024-07-25 17:06:00
categories: 前端
---
在XPath中，可以使用不同的函数和运算符来选择元素名称相等和包含特定字符串的元素。

<!-- more -->

### 1\. 选择元素名称相等的元素

如果你想选择元素名称等于特定值的元素，可以使用标准的XPath语法。例如，要选择名称为 `div` 的所有元素：

```javascript
//div
```

### 2\. 选择元素名称包含特定字符串的元素

要选择元素名称包含特定字符串的元素，可以使用 `contains()` 函数结合 `name()` 函数。`name()` 函数返回节点的名称，`contains()` 函数检查一个字符串是否包含另一个字符串。

```javascript
//*[contains(name(), 'div')]
```

### 3\. 选择元素属性值相等的元素

如果你想选择某个属性值等于特定值的元素，可以使用 `@` 符号。例如，选择 `class` 属性等于 `example` 的元素：

```javascript
//*[@class='example']
```

### 4\. 选择元素属性值包含特定字符串的元素

要选择某个属性值包含特定字符串的元素，可以使用 `contains()` 函数。例如，选择 `class` 属性包含 `example` 的元素：

```javascript
//*[contains(@class, 'example')]
```

### 5\. 选择元素文本内容包含特定字符串的元素

如果你想选择文本内容包含特定字符串的元素，可以使用 `contains()` 函数结合 `text()` 函数。例如，选择文本内容包含 `example` 的元素：

```javascript
//*[contains(text(), 'example')]
```

### 6\. 选择文本内容等于特定值的元素

如果你想选择文本内容完全等于特定字符串的元素，可以使用 `text() = 'value'` 这种方式。

```javascript
//*[text() = 'example text']
```
