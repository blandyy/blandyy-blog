---
title: xpath怎么过滤元素
date: 2024-01-15 01:46:01
categories: 前端
---
要使用XPath过滤掉属性不等于某种名称的元素，可以使用not()函数和@class属性来实现。

<!-- more -->

JavaScript

```javascript
//div[not(@class='aaa')]  // XPath表达式，过滤掉class不等于aaa的div元素
```

它将匹配具有class属性不等于aaa的div元素。not()函数用于否定条件，@class表示class属性。

