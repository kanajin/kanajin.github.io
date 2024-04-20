---
title: '群晖nas本地部署Bitwarden服务器管理密码'
date: 2024-04-20T16:47:45+08:00
draft: false
categories: ["生活"]
tags: ["工具"]
#description=
---

> 本笔记记录如何使用docker部署bitwarden服务端和acme自签证书，请保证前置条件：
> 1.已经购买一个域名
> 2.关闭群晖的二步验证(我没有将群晖放到公网，内网访问环境下不需要二步验证)
> 3.在路由器中把nas的内网ip设置成固定ip

## 部署bitwarden服务端

### 安装并启动镜像

官方提供的docker部署方案面向企业，功能更全面但对资源的要求更高，镜像也大得多

对于个人使用[vaultwarden](https://github.com/dani-garcia/vaultwarden)即可。[中文wiki](https://rs.ppgg.in/home)

1. 在群晖container manager中搜索`vaultwarden`，安装`vaultwarden/server:latest`
2. 把端口映射保持默认即可，即容器内的`80`和`3012`端口需要映射出来~~虽然3012端口我不知道有什么用，但默认给了就映射一下吧~~。注意nas上的80端口可能会被占用。
    80端口映射出来的端口不直接用，后面用反向代理。
3. 存储空间(volume)选一个用来存放数据库的文件夹，映射到容器内的`/data`，读写权限都开

点击启动即可，但现在还没配置http所以只能打开登录界面，不能正常使用

### 配置反代

1. 群晖：控制面板-登陆门户-高级-反向代理，如果找不到直接在控制面板搜索反向代理
2. 新增一项
   - 来源协议选`https`，端口自己指定，主机名保持`*`
   - 目的地协议选`http`，端口为刚刚在容器中映射出来的host端口，主机名为localhost

然后访问`https://nasip:<反代https端口>`来使用

此时已经可以注册并登录了，但浏览器会提示证书问题，接下来安装证书

## 自动签名与安装证书

### 添加解析

给自己的域名中添加一条`A类型解析`，ip地址为nas的内网ip

### 部署自签服务

自签证书只能签域名，先在自己的域名中

1. container manager搜索`acme`，安装`neilpang/acme.sh:latest`，[镜像文档](https://github.com/acmesh-official/acme.sh/wiki/Run-acme.sh-in-docker)
2. 设置几个环境变量
   - AUTO_UPGRADE: 1
   - SYNO_Username: <nas用户名>
   - SYNO_Password: <nas密码>
3. 创建一个文件夹，存放acme的配置
   - 在改文件夹下新建一个文本文档`account.conf`，内容如下，以腾讯云为例，注意不能照抄

        ```conf
        export DP_Id="域名解析商提供的TOKENid"  
        export DP_Key="域名解析商提供的TOKEN"
        AUTO_UPGRADE='1'    # 所有提供商都加这行，不加可能会遇到神奇的bug，反正加了也没问题
        ```

        各个域名提供商的api用法查阅[acme wiki](https://github.com/acmesh-official/acme.sh/wiki/dnsapi)

        每个提供商在这里export的东西不一样，请查看wiki

        每个提供商注册api token的方式也不同，去对应官网搜索`api`或者`token`之类的就能找到

   - 将该文件夹映射到容器的`/acme.sh`里，读写权限都开
   - 在执行命令中填入`daemon`
   - **容器名要记住**，不需要其他配置，点运行

### 启动自动签名脚本

1. 在刚刚存放acme配置的文件夹下，新建一个.sh的脚本，注意替换尖括号中的容器名称，内容如下，不要照抄

    ```shell
    #!/bin/bash

    docker exec <替换成你自己的容器名字> acme.sh --force --log --issue --server letsencrypt --dns dns_dp --dnssleep 120 -d example.com -d xx.example.com

    docker exec <替换成你自己的容器名字> acme.sh --deploy -d example.com -d xx.example.com --deploy-hook synology_dsm
    ```

    解释(按行)：
    1. 申请证书，最后的两个`example.com`和`xx.example.com`替换成自己的域名，这个域名是你用来访问bitwarden的域名
    2. 部署证书到nas，两个域名记得替换，最后的`--deploy-hook synology_dsm`会读取容器环境变量的`SYNO_Username`和`SYNO_Password`，且会被两部验证影响，注意

2. 群晖：控制面板-任务计划，找不到就搜
3. 新增-计划的任务-用户定义的脚本
   - 常规：把用户账号调成`root`
   - 计划：选在以下日期运行，然后重复调成每个月
   - 任务设置里运行命令输入：bash /volume1/docker/acme/autocert.sh >>/volume1/docker/acme/log.txt 2>&1
        注意：`/volume1/docker/acme/`这个路径就是刚刚提到的存放acme配置的文件夹，寻找的方法为群晖：File Station-右击文件-属性-位置，**自行替换**

        `autocert.sh`是刚刚创建的.sh脚本，名字按自己的来

        `log.txt`是存放日志的，不需要自己创建，前面的路径要对
   - 通知设置可填可不填，已经打了log了必要性不是很大，点击确定即可
4. 选中任务，点运行先执行一次，大概需要2分钟左右，不出意外的话证书就安装到群晖机器上了，出意外的话记得看log
5. 群晖：安全性-证书中可以看到以自己域名命名的证书，就像：`example.com`。点击设置能看到一个名叫*:<端口>的服务，把它的证书设置为该证书即可。或者可以把所有证书都设置成这个

## 使用

完成部署之后，访问`https://<解析成内网的域名>:<反代端口>`就可以看到vaulwarden的控制台了，注册并登录，正常使用即可

bitwarden是通过一个主密码管理所有的密码，所以你**只需要也必须要**记住主密码

客户端也是访问这个url连接服务器，记得选自搭建
