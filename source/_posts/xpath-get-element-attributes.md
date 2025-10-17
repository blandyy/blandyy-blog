---
title: xpath获取元素属性
date: 2024-01-05 01:50:19
categories: 前端
---
要使用XPath获取元素的属性，可以使用@符号后跟属性名称。

<!-- more -->

JavaScript

```javascript
//div[@class='example']/@data-id  // XPath表达式，@data-id是属性名称
```

它将匹配具有class属性为example的div元素。@data-id部分表示要获取的属性名称。
