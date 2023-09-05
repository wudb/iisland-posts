---
title: "Obsidian写博客文章二：自动构建问题"
tags: [obsidian,blog]
date: 2023-08-29 18:09:29
draft: false
hideInList: false
isTop: false
feature:
---

> 针对[[obsidian-write-blog-1]]最后遗留的问题：多仓库同步并自动构建的问题的解决方案，记录于此文。

## 原因
博客实际经常变动的部分只有 content，又不想 blog 和普通笔记分开两个obsidian 库，所以实现了这个变体。两个库的问题是ob 插件也是两份，设置也是两份。