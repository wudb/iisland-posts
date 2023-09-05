---
title: "Obsidian写博客文章二：自动构建问题"
tags: [obsidian,blog]
date: 2023-08-29 18:09:29
draft: true
hideInList: false
isTop: false
feature:
---

> 针对[Obsidian写博客文章一：基础工具构建 - iIsland](https://iisland.pages.dev/posts/obsidian-write-blog-1/)最后遗留的问题：多仓库同步并自动构建的问题的解决方案，记录于此文。

## 结构
博客实际经常变动的部分只有 content，又不想 blog 和普通笔记分开两个obsidian 库，所以最终形成了这样一个结构：

	博客文章库->iisland-posts
	博客基础库->iisland，posts 作为其子库
	普通笔记库->notes，posts 作为其子库

那么平时写博客就需要在 notes 库中进行就可以了，而且这样的结构的一个好处是所有的笔记（包括博客文章）都统一管理，可以统一备份到一个地方。

## 问题
如果只有一个库，那就是配置好这个库的 ob插件和设置就可以通过 obsidian git 插件自动 push，然后cloudflare 自动构建发布就 OK 了。
现在这种结构下 submodule 的更新，母库不知道，所以就没法实现自动 push，自动构建发布文章了。

## 解决方案
实现方案主要基于 Github 的 workflow，实际上是通过监听子库的 push 动作，然后子库通过 Github 接口发送*repository_dispatch*事件，母库接收子库发出的事件，然后更新