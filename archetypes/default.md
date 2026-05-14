+++
title = "{{ replace .File.ContentBaseName "-" " " | title }}"
date = "{{ .Date }}"
slug = "{{ .File.ContentBaseName }}"
description = "这里填写文章摘要。"
categories = ["技术"]
tags = ["Hugo"]
draft = true
toc = true
comments = true
math = false
readingTime = true
+++

这里写文章开头，可以先用一两句话说明这篇文章要解决什么问题。

## 背景

这里写为什么要写这篇文章，或者问题是怎么出现的。

## 正文

这里写主要内容。

```text
这里可以放命令、配置或代码片段。
```

## 总结

这里写结论、踩坑记录或后续计划。
