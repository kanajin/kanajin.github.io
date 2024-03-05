---
title: "使用Hugo搭建博客"
date: 2024-03-02T14:51:02+08:00
draft: false
tags: ["工具", "建站"]
categories: ["生活"]
description: "简要介绍使用Hugo搭建个人博客并由github托管"
---

> 当前博客使用PaperMod主题，使用github托管发布。  
> 源文件仓库位于[https://github.com/kanajin/hugo-blog-source](https://github.com/kanajin/hugo-blog-source)  
> 初期学习时提交过多无用commit，望海涵  
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

## 3. 指定主题

Hugo的站点必须安装主题才能够使用，因此需要先挑选一个合适的主题

主题下载站：[https://themes.gohugo.io/](https://themes.gohugo.io/)，或是中文网站：[https://www.gohugo.org/theme/](https://www.gohugo.org/theme/)。选择好心仪的主题后，根据网站中的引导可以进入主题的GitHub仓库

Hugo站点的主题需要将主题代码目录放进 `/themes/`目录中，并在 `config.yaml` 中指定主题，格式为`theme: [<theme_name>]`

主题的安装方式在主题仓库中均有文档，这里不再赘述，具体主题安装心得参见[施工中]

## 4. 配置文件

config.yaml的[官方文档](https://gohugo.io/getting-started/configuration/)

大部分配置是通用的，少数配置参数需要参考主题文档调整。下面是我的部分配置和一些说明

```yaml
# If your site is in 'https', then make sure your base url isn't written using 'http' otherwise your sitemap would
# contain http (as opposeed to https) URLs. This would affect Google indexing of your URLs.
baseURL: "https://kanajin.github.io"    # 网站域名
title: "Alea jacta est!"    # 网站标题
paginate: 5     # 每页显示的内容项数
theme: [PaperMod]   # 主题

enableInlineShortcodes: true    # 启用内联短代码
enableRobotsTXT: true   # 生成robot.txt用于指导爬虫
buildDrafts: false    # 若文章被标记为“草稿”，则不会发布在网站上
buildFuture: false    # 若文章的发布日期设置在未来，则不会发布在网站上
enableEmoji: true
pygmentsUseClasses: true    # 可以通过CSS设置代码样式
mainsections: ["posts"]     # 生成主页时以/posts/目录下的项目为主
defaultContentLanguage: "zh"    # 设置默认语言

languages:      # 语言配置
  zh:
    languageName: "简体中文"
    weight: 1
    taxonomies:     # 定义分类法，在文章头部可以指定文章的相关分类，Hugo会识别并自动帮你分类
      category: categories
      tag: tags
      series: series
    menu:       # 菜单项配置
      main:
        - name: 归档
          url: archives
          weight: 1     # 权重，越低越靠前
        - name: 搜索
          url: search/  # 主题配置
          weight: 2
        - name: 分类
          url: categories   # 分类法定义的分类
          weight: 3
        - name: 标签
          url: tags     # 分类法定义的分类
          weight: 4

outputs:    # 指示构建网页时应该输出哪些文件
  home:
    - HTML      # 网页基本构成
    - RSS       # 用于订阅
    - JSON      # 用于爬虫

params:     # 为主题配置，需参考主题文档
  env: production
  author: 铃兰怜雪
  ...
```

## 5. 构建网站

在项目根目录下执行 hugo 命令可以在本地启动服务器，以下是一些常用命令与解释

```zsh
hugo server                 # 最常用的命令，采用hugo默认配置构建页面，默认监听 localhost:1313
hugo server -D              # 即使被标记为“草稿”的文章也会被发布
hugo server --bind 0.0.0.0  # 监听0.0.0.0，方便在局域网其他设备上启动服务
hugo                        # 在根目录生成public/目录，将构建的网站内容放进其中
hugo --cleanDestinationDir  # 会先清除public/目录，再重新构建，即清除缓存
                            # 后两个命令不会启动服务
```

## 6. 撰写博客

使用`hugo new <path/to/article_name.md>`可以在`/content/path/to/`目录生成一个.md文件，用于撰写你的博客

.md文件会自带类似下面的内容：

```markdown
---
title: "some title"
date: a date
draft: true
tags: ["tag1", "tag2"]
categories: ["categories1"]
description: "some description"
---
```

具体内容可以在`/archetypes/default.md`中设定，这个也是上文提到的`文章头`，请保留。更多设置参考[官方文档](https://gohugo.io/content-management/front-matter/)

使用`markdown`语法来撰写博文，hugo会帮助你渲染成HTML并发布在站点上。简单的markdown语法规则可以阅读[菜鸟教程](https://www.runoob.com/markdown/md-tutorial.html)

## 7. 交给GitHub托管

施工中
