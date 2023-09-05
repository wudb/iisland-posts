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
实现方案主要基于 Github 的 workflow，通过监听子库的 push 动作，子库通过 Github 接口发送*repository_dispatch*事件。母库接收子库发出的事件，更新子库。

**iisland-posts(子库) workflow**
```yaml
name: Blog Content Update Trigger
on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Trigger parent repository update 
        run: |
          curl -L \
            -X POST \
            -H "Accept: application/vnd.github+json" \
            -H "Authorization: Bearer ${{ secrets.ACCESS_TOKEN }}" \
            -H "X-GitHub-Api-Version: 2022-11-28" \
            # OWNER/REPO 替换成自己的仓库 s
            https://api.github.com/repos/OWNER/REPO/dispatches \  
            -d '{"event_type":"content_update_trigger","client_payload":{"message": "${{ github.event.head_commit.message }}" }}'
```

**iisland(母库) workflow**

```yaml
name: Blog Content Updater
on: 
    repository_dispatch:
        types: [content_update_trigger]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Event Information
        run: |
          echo "Event '${{ github.event.action }}' received from '${{ github.event.client_payload.message }}'"
      - name: Checkout repository
        uses: actions/checkout@v3
        with:
          token: ${{ secrets.ACCESS_TOKEN }}
          submodules: true
      - name: Pull submodule
        run: |
          git pull --recurse-submodules
          git submodule update --init --recursive --remote
      - name: Push changes
        run: |
          git config --global user.name 'name'
          git config --global user.email 'xxx@gmail.com'
          git add .
          git commit -m "${{ github.event.client_payload.message }}"
          git push
```

secrets.ACCESS_TOKEN 的设置
Github 的项目 settings 里面，找到 Secrets and variables里的 Actions，点击 New repository secret，Name 里面填入*ACCESS_TOKEN*，Secret 里面填入Personal Access Tokens

如此，结合 Obsidian Git 插件就可以实现自动提交，自动构建，发布文章了。

## 调试