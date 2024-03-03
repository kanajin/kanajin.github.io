---
title: "使用Hugo搭建博客"
date: 2024-03-02T14:51:02+08:00
draft: true
tags: ["工具", "建站"]
categories: ["生活"]
description: "简要介绍使用Hugo搭建个人博客并由github托管"
---

> 当前博客使用PaperMod主题，使用github托管发布。  
> 源文件仓库位于[https://github.com/kanajin/hugo-blog-source](https://github.com/kanajin/hugo-blog-source)  
> 初期学习时提交过多无用commit，见谅  
> hugo quick start：[https://gohugo.io/getting-started/quick-start/](https://gohugo.io/getting-started/quick-start/)

## 1. 安装Hugo

可以直接前往[Hugo官网下载](https://gohugo.io/installation/)查看说明

此外各操作系统均可使用包管理器安装

- Windows：scoop

```powershell
scoop bucket add extras
scoop install hugo
```

Linxu与macOS不再赘述

安装成功后使用命令`hugo version`确认安装情况

## 2. 建站

使用命令`hugo new site <name>`来新建一个博客站点，可以在命令中指定博客配置文件的格式，例如

```zsh
hugo new site my-new-blog                # 配置文件为hugo.toml
hugo new site my-new-blog --format yaml  # 配置文件为hugo.yaml，推荐，yaml格式更加易读
```

这个命令会在终端的当前目录下新建一个名为`my-new-blog`的目录，并部署好Hugo所需的资源，目录结构大致如下所示：

```zsh
$ tree
.
├── archetypes
│   └── default.md  # 文章头部模板，新建博文时会使用这里的文章头部配置
├── assets
├── content         # 用于存放博文
├── data
├── config.yaml     # 配置文件，新版中可能叫做hugo.yaml，本文以config为准
├── i18n
├── layouts         # 存放模板文件，指导Hugo如何将content目录中的markdown渲染成HTML
├── static          # 用于存放静态文件，如图片、CSS、JavaScript 文件等，建议使用git lfs管理
└── themes          # 用于存放主题

9 directories, 2 files
```

Hugo必须指定主题
