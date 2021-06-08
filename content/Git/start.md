---
title: "初次使用Git"
date: 2021-06-07T13:13:28+08:00
draft: false
categories: ["Git学习"]
---

# 初次使用git：
提交用户信息，通过以下两行指令提交用户名和邮箱（蓝色字体为自定义输入）  
```
$ git config --global user.name "Suzuki"
$ git config --global user.email ekanajin@gmail.com
```

*注意：邮箱建议和你的代码托管网站所用邮箱相同，否则commit会有问题*   
		
通过以下命令来确认是否提交：  
```
$ git config –list
user.name= Suzuki
user.email=ekanajin@gmail.com
```

这个命令会返回很多变量，也可以用
`git config <key>`
指定返回变量：  
```
$ git config user.name
user.name=Suzuki
```

在想要建立git仓库的空目录下执行  
```
$ git init
```

如果是一个非空文件夹，通过 git add 命令添加所需文件，并提交。例如：  
```
$ git add *.c
$ git add LICENSE
$ git commit -m ‘say something’
```

# 添加SSH公钥（使用ed25519加密）：  
	ssh-keygen -t ed25519 -C "Suzuki "

然后一路回车（会要求输入密码，一般不需要，回车即可）
然后就会在`~/.ssh/`目录下生成两个文件，公钥`id_ed25519.pub`和私钥`id_ed25519`，公钥用于粘贴到代码托管网站，私钥一定不能给别人。

# clone已有仓库：
在代码托管网站，找到SSH地址，然后输入以下命令（标蓝为SSH地址）  
```
$git clone git@github.com:<your_github_username>/repositories.git
```