---
title: "Obsidian写博客文章"
tags: obsidian, blog
date: 2023-08-29T14:02:53+08:00
draft: false
image-auto-upload: true
---

>缘由：不想使用多个写文章的工具，想集中到 Obsidian 中统一写，所以才有了这次的文章。此文只是记录研究的过程，以便后查。

## Hugo
- mac 下安装 hugo 比较简单
```sh
brew install hugo #安装
hugo version #查看安装是否成功
```
- 创建 blog，在指定的目录下执行
```sh
hugo new site iisland #创建 blog
cd iisland #进入文件夹
```
- 选择[主题](https://themes.gohugo.io/)
```sh
git submodule add https://github.com/ph-ph/chalk.git themes/chalk
```
- 修改 hugo.toml
```sh
baseURL = 'https://iisland.pages.dev/'
languageCode = 'zh-CN'
title = 'iIsland'
theme = 'chalk'
```
- 创建一篇文章，写入文章内容，注意：*文件名不能有中文和空格*
```sh
hugo new content posts/my-first-post.md
```

## Github
- 创建私有仓库 iisland
- 将本地 blog文件夹 iisland 上传的 github

## Cloudflare
- 登录后选择 Workers 和 Pages下的*创建应用程序*
- 选择Pages，连接到 github，选择仓库 iisland
- 构建选择 hugo就可以了
- 环境变量添加*HUGO_VERSION=0.117.0*
- 然后就等着构建，等几分钟后去[iIsland](https://iisland.pages.dev/)看看是否成功

## Obsidian
- 新建 blog 模板，模板内容
```yaml
---
title: {{NAME}}
tags: [{{VALUE:tag？}}]
date: {{DATE:YYYY-MM-DD HH:mm:ss}}
draft: true
hideInList: false
isTop: false
image-auto-upload: true  #图片自动上传开启
feature:
---
```
- 安装插件QuickAdd，设置如下
输入框中输入 new blog，点击 Add Choice，就如下：
![image.png](https://images-1256934664.cos.ap-nanjing.myqcloud.com/notes/20230830162606.png)

点击 new blog 的设置按钮，设置参照如下：
![image.png](https://images-1256934664.cos.ap-nanjing.myqcloud.com/notes/20230830162633.png)

使用：command+P，输入 QuickAdd，然后输入 new blog即可创建文章了

- 图片管理主要使用PicGo 和 Image Auto Upload Plugin插件，设置完成后粘贴就会自动上传和显示

## 变体
> 不想 blog 和普通笔记分开两个obsidian 库，所以实现了这个变体。两个库的问题是ob 插件也是两份，设置也是两份

- 将 iisland中的 content 文件夹单独成一个github 仓库iisland-posts(需要将 iisland-posts 设置成 Public，否则 cloudflare 不能读取)
- 然后将 iisland-posts 仓库作为 iisland 和 普通笔记仓库的 submodule
- 这样普通笔记这边共享了插件和设置，ob写完文章提交到 iisland-posts 仓库
- iisland 仓库拉取最新子模块 iisland-posts，然后提交更新到 iisland，cloudflare 将自动构建

## 问题
将博客文章单独成子仓库后，每次提交的步骤有些繁琐，需要先提交子仓库，再在博客仓库更新子仓库后再提交更新。本可以通过使用*Obsidian Git*插件自动提交到 github，现在暂未实现。

#### 参考
[将Hugo博客部署到Cloudflare Pages上的体验与踩坑小记](https://tutuis.me/deploy-blog-to-cloudflare-pages/)  
[obsidian配合hugo、cloudflare：让发布博客简单到不可思议 :: 夜猫日记](https://lillianwho.com/posts/obsidian-hugo-cloudflare/)   
[Hugo 博客写作最佳实践 | 胡说](https://blog.zhangyingwei.com/posts/2022m4d11h19m42s28/)  
