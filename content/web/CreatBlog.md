---
title: "使用 Github Pages 搭建个人博客"
date: 2021-06-08T23:22:43+08:00
draft: false
categories: ["网站搭建"]
enableMathJax: false
---

> 仿佛越级打歌一般以零基础的底气决定建一个在线Blog  
> 经历了10%的自己摸索+90%的L君帮助下，Blog算是磕磕碰碰搭起来了  
> 特此记录下捣鼓的过程，让这段时间学到的东西切实称为自己的东西  
> 顺便表示对L君的感谢

# 使用Hugo搭建静态网站
~~简洁，强大，无脑，安心~~
## 安装Hugo
### 如果你是`macOS`用户或者`Linux`用户，可直接用包管理器安装
### 如果你是`Windows`用户，访问Hugo的github[官网release](https://github.com/gohugoio/hugo/releases)
下载成功一个zip压缩包，在你喜欢的地方解压，然后将含有`hugo.exe`的目录添加进`PATH`环境变量中，启动WindowsTerminal，输入以下指令：  
```
hugo version
```
如果成功，则终端将显示以下内容：*这里以撰写文章时的最新版本为例*
```
$ hugo version
hugo v0.83.1-5AFE0A57 windows/amd64 BuildDate=2021-05-02T14:38:05Z VendorInfo=gohugoio
```

> 可能遇到的问题：  
> * 终端反馈无法识别`hugo`指令，原因是存放`hugo.exe`的目录未成功添加至`PATH`中，在终端输入：
> ```
> write-host $env:Path
> ```
> 
> 　　看看输出中是否有该目录，如果没有，重新加入，重启终端。

## 使用Hugo建立一个新网站实例
当Hugo安装成功后，就可以创建一个网站目录了  
在任意位置创建一个存放网站的目录，以`D:\WebSite`为例，在终端下输入：  
```
hugo new site D:\WebSite
```
hugo将会在D盘自动生成一个名为WebSite的文件夹，里面存放了建立网站需要的资源。包括`content`目录以及`config.toml`文件，前者用于存放你写的md文件，后者则是网站的配置文件。
## 建立本地仓库并连接到github
如果要使用github托管网站，现在就先将这个网站文件夹建成git仓库。在当前目录下使用命令`git init`建立仓库，之后的步骤与[初次使用Git]({{< ref "Git/start.md" >}})这篇文章相同，不再赘述。
## 下载一个主题
Hugo必须选定主题后才能正常使用，因此先去主题站<https://themes.gohugo.io>选择一个你喜欢的主题，并前往该主题的github页面，根据`README`的指导安装主题，命令行格式为：
```
git submodule add https://github.com/<theme_name>
```
安装完成后，在`config.toml`里添加一行：`theme = "<theme_name>"`，主题就安装成功了。  
**注意！一次只能安装一个主题！否则会出错**
> 若你不想在github上部署你的网站，直接在release中下载主题然后解压到网站目录下的`themes`文件夹中即可
## 撰写并发表你的第一篇文章
终端cd到网站目录下，使用命令：`hugo new`来创建一篇markdown文章，具体格式为：
```
hugo new 文件夹名\文章名.md
```
该命令会在`content`文件夹下创建一个新的文件夹，新文件夹名为你输入的名字（如果没有指定文件夹名，则markdown文件直接生成在content目录下）。
你会看到markdown文件头部有以下内容：
```
---
title: "文件名"
date: <date>
draft: true
---
```
> 从上到下指定了文章的标题、日期和草稿状态，可以根据自己的实际需要修改。  
> 你也可以在网站目录下的`archetypes`文件夹中找到`default.md`文件，在其中删除、添加和修改修改默认表头。  

当你完成了你的文章并保存后，在网站目录下使用终端命令：`hugo server -D`即可生成本地预览网站，在浏览器中输入`http://localhost:1313`即可访问生成的网站。
> `-D`参数表示连同草稿一起发布，如果没有指定`-D`参数，则只有表头属性为`draft: false`的文章才会被发布

最后别忘了在`config.toml`中把`title`改成你喜欢的名字。
# 使用github托管你的网站
1. 在github中新建一个仓库  
仓库名字严格使用<你的github名字>.github.io，这是托管网站的前提条件。  
2. 在网站目录下新建一个文件夹`.github`，在其中创建一个新文件`main.yml`并填写如下内容：

```
name: github pages

on:
  push:
    branches:
      - master  # Set a branch to deploy
  pull_request:

jobs:
  deploy:
    runs-on: ubuntu-20.04
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true  # Fetch Hugo themes (true OR recursive)
          fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
          # extended: true

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        if: github.ref == 'refs/heads/master'
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```
3. 保存后将本地网站仓库push到github仓库上，在github仓库下选择标签`Actions`，脚本自动开始运行
> 此处内容编写借鉴了[Hugo官方文档](https://gohugo.io/hosting-and-deployment/hosting-on-github)以及P3TERX佬的这篇教程[GitHub Actions 入门教程](https://p3terx.com/archives/github-actions-started-tutorial.html)，最后yml文件由L君编写，~~跪谢。~~
## 最后一步
当Actions运行成功后，你的仓库应该多了一个名为`gh-pages`的新分支。转到仓库的设置界面，在page标签下，Sorce中选择这个新分支，点击Save，上方出现如下提示：  
![image](/CreateBlog/CreateSuccess.jpg)  
恭喜！你的博客已经搭建成功！访问一开始设置的域名`https://<你的github名字>.github.io`访问吧！
> 搭建过程中遇到的问题与解决  
> 施工中