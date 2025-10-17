---
title: 在谷歌浏览器的控制台中怎么判断一个网页是否加载完成
date: 2024-07-02 01:23:05
categories: 前端
---

使用document.readyState属性

document.readyState 在浏览器中通常表示文档的加载状态，其值可以是以下几种：

<!-- more -->

1.  "loading"：文档正在加载。

2.  "interactive"：文档已被解析，除了图片、样式表和iframe之外的资源都已完成加载，此时用户可以与页面交互。

3.  "complete"：文档和所有子资源已完成加载。

