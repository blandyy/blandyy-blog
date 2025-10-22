---
title: python将markdown转换为html并应用github-markdown-css样式
date: 2023-09-26 13:13:07
categories:
- 代码
- Python
---
最近在尝试给文本编辑器加一个markdown转html的功能，然后发现转换出来的样式很丑，遂使用github-markdown-css样式进行美化。

主要使用python-markdown模块来进行转换，并使用开源的github-markdown-css样式来美化

<!-- more -->

## 1.安装markdown库

```bash
pip install markdown
```

## 2.markdown使用方式

样例md文件内容：

````markdown
# 测试
## 表格
| key     | value |
| ------- | ----- |
| version | 1.0.0 |
```python
import markdown
html = markdown.markdown(your_text_string)
```
> 引用
正文--------------------------------------
````

转换代码：

````python
import markdown
md_str = """# 测试
## 表格
| key     | value |
| ------- | ----- |
| version | 1.0.0 |
```python
import markdown
html = markdown.markdown(your_text_string)
```
> 引用
正文--------------------------------------"""
html = markdown.markdown(md_str)  # 将markdown字符串转为为html字符串
# 将生成的html代码应用css，注意需要用<article class="markdown-body">标签包住
html_style = f"""<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/github-markdown-css/5.2.0/github-markdown.min.css">
</head>
<body>
<article class="markdown-body">
{html}
</article>
</body>
</html>
"""
with open("D:/test_md2html.html", "w", encoding="utf-8") as f:
    f.write(html_style)
````

## 3.转换时引入扩展

但是我们发现，表格和代码没有高亮，只应用了css的基本样式，这是因为转换的时候没有增加表格和代码高亮的插件，详细信息请看文档：[python-markdown扩展文档](https://python-markdown.github.io/extensions/)

基于上面的文档，我们将代码修改如下：

````python
import markdown
md_str = """# 测试
## 表格
| key     | value |
| ------- | ----- |
| version | 1.0.0 |
```python
import markdown
html = markdown.markdown(your_text_string, extensions=["tables", "fenced_code"])  # 将markdown字符串转为为html字符串
```
> 引用
正文--------------------------------------"""
html = markdown.markdown(md_str)
# 将生成的html代码应用css，注意需要用<article class="markdown-body">标签包住
html_style = f"""<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/github-markdown-css/5.2.0/github-markdown.min.css">
</head>
<body>
<article class="markdown-body">
{html}
</article>
</body>
</html>
"""
with open("D:/test_md2html.html", "w", encoding="utf-8") as f:
    f.write(html_style)
````

其中extensions=\["tables", "fenced\_code"\]就是表格和代码高亮扩展。

展示效果很完美。

## 4.总结

先通过python-markdown将md字符串转换成html代码，通过将其包在容器中应用github-markdown-css的样式，css文件是通过cdn导入的，如果有需要可以访问官方仓库将css下载到本地。

感谢开源的贡献：

[python-markdown官方文档](https://python-markdown.github.io/)

[github-markdown-css的仓库](https://github.com/sindresorhus/github-markdown-css)
