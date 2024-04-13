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
hugo new site myname.github.io                # 配置文件为hugo.toml
hugo new site myname.github.io --format yaml  # 配置文件为hugo.yaml，推荐，yaml格式更加易读
```

这个命令会在终端的当前目录下新建一个名为`myname.github.io`的目录，并部署好Hugo所需的资源，目录结构大致如下所示：

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

> 这里要注意站点的名称必须是`<自定义英文>.github.io`的格式，才能被正确托管

## 3. 指定主题

Hugo的站点必须安装主题才能够使用，因此需要先挑选一个合适的主题

主题下载站：[https://themes.gohugo.io/](https://themes.gohugo.io/)，或是中文网站：[https://www.gohugo.org/theme/](https://www.gohugo.org/theme/)。选择好心仪的主题后，根据网站中的引导可以进入主题的GitHub仓库

Hugo站点的主题需要将主题代码目录放进 `/themes/`目录中，并在 `config.yaml` 中指定主题，格式为`theme: [<theme_name>]`

主题的安装方式在主题仓库中均有文档，自行查阅

> 个人建议的维护主题的方式为：
> 将主题仓库fork一遍，并在fork仓库中做自己的修改，然后博客站安装自己仓库的主题

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

## 7. 由Github自动生成网站并发布

通过`Github workflow`，可以自动将写好的markdown文件编译成静态网页并发布到`Github page`，让自己只需要专注写内容即可，可谓一劳永逸

### 申请Token

由于每次编译网页并托管给`Github page`都需要验证身份，因此需要先申请一个token，并自动把token传进去做验证

申请token的路径是：github - Settings - 最下面的Developer Settings - Personal access tokens - Tokens(classic)

注意同级还有另一个选项：Fine-grained tokens，并不能用于workflow，不要申请错了

申请会要求你输入一个名字，这里我们假设名字是`HUGO_KEY`，成功后会给你一个TOKENID，复制并保存好，**以后看不到了**

### 写workflow文件

在项目根目录进行操作

```zsh
$ myname.github.io > mkdir -p .github/workflows/
cd .github/workflows
touch main.yml
```

在创建的`main.yml`中输入如下内容

```yaml
name: Build and Deploy Hugo Site

on:
  push:
    branches:
      - master

jobs:
  build-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2
        with:
          submodules: 'recursive'
          lfs: true

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'

      - name: Build Hugo Site
        run: hugo --minify

      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.HUGO_KEY }}   # 这里把HUGO_KEY换成你的TOKEN名字
          publish_dir: ./public
```

完成这些之后，你可以写一篇文章，然后尝试发布

直接push仓库，如果一切顺利的话你的仓库会多一个名为`gh-pages`的分支，然后在项目的设置里找到`Pages`，在`Build and deployment`里面将`Branch`设置为`gh-pages`，等一段时间应该就能发布自己的博客了

博客的地址就是`https://myname.github.io`
