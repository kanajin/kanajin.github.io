---
title: "安装archlinux双系统"
date: 2021-06-17T14:13:59+08:00
draft: false
categories: ["操作系统"]
enableMathJax: false
---

> # 前言：
> 原本打算购置一台macbook来体验类Unix系统的开发环境，谁知今年的WWDC实在令人有些失望，最后转而买了一块ssd来打算装archlinux玩。在L君的帮助下花了两天时间总算是弄下来了(说，谢谢L君)，以下记录一下折腾的过程。

参考了的[安装指南](https://wiki.archlinux.org/title/Installation_guide)和知乎用户“飞我去月球”的文章[2021.02. Arch Linux 安装小记](https://zhuanlan.zhihu.com/p/348370646)。
# 准备工作
## 分配磁盘
*首先默认已经拥有可用的Windows系统*
### 使用原有磁盘
如果你打算在你正在使用的磁盘上安装Linux...说实话我不知道，但猜想应该通过分区工具获得一块连续的分区空间，可以和下文的新磁盘同样的操作步骤，关于分区工具的使用，我没有系统地学习过，以后如果用上了再写总结。
### 使用全新磁盘
提前备好塑料片和螺丝刀(我忘了买塑料片上了金属555幸好没刮花)，安装好之后右键计算机-管理-储存-磁盘管理，找到新装上去的磁盘。此时Windows会提醒你格式化，先不格式化，确认识别到了就行。
## 制作启动U盘
首先下载ArchLinux辅助安装系统：[下载链接](https://archlinux.org/download/)  
注：往下翻到`HTTP Direct Downloads`这一栏，然后选择国内的链接就行。  
然后下载Windows下的PE制作工具，我个人使用Etcher。[官网链接](https://www.balena.io/etcher/) | [Github链接](https://github.com/balena-io/etcher)  
选择下载的iso镜像以及U盘，制作即可。
## 进入PE系统
启动U盘制作完成之后，就可以重启电脑了。进入BIOS选择U盘启动，即可看到命令行界面(液，你已经装好Linux了(并不))。
> 在这里我遇到了无法使用U盘启动的问题，原因BIOS是没有开启允许`external drive`引导，在BIOS中找到`Secure Boot`选项，将值设为`disable`即可。

## 连接网络
此处分为有线网与无线网，强烈建议使用有线网，简单方便。我安装过程使用的有线网，关于无线网连接的方式参考知乎文章。
### 有线网
~~插上网线，完了~~  
~~不是，还得ping一下baidu.com看看~~  
插上网线，输入`ip link`，输出值中有一个 *** state up的，是一个很奇怪的名字，就是有线网卡的名称，说明有线网卡已经起了。  
然后输入`ip addr`获取ipv4地址，返回值中的`inet`即为ipv4地址，成功后输入`ping baidu.com`看是否连通(记得ctrl+C退出)。
### 无线网络
Archiso默认使用iwd作为无线网络管理器。  
命令行输入`iwctl`进入iwd命令行  
`[iwd]#`长这样  
输入命令：`[iwd]# device list`以查询网卡设备。通常无线网卡名称是wlan0，在有线网一栏`ip link`也可以看到它。  
使用这个命令扫描WiFi：`[iwd#] station <devicename> scan`，假设查到的无线网卡名称为`wlan0`，则命令为：`[iwd#] station wlan0 scan`  
输出扫描结果：`[iwd#] station <devicename> get-networks`  
找到要链接的WiFi的SSID，并输入：`[iwd#] station <devicename> connect <wifi-ssid>`，然后根据提示输入密码即可。  
退出iwd命令行：`[iwd#] exit`  
然后执行：`# dhcpd`分配ip，并`ping baidu.com`看一下网络是否连上。
## 更新系统时间
`# timedatectl set-ntp true`  
验证时间是否正确：  
`# timedatectl status`  
## 硬盘分区
使用命令`fdisk -l`来列出所有磁盘，在这里也可以找到Windows系统的磁盘。
> 一般来说：  
> /dev/sd 指非NVMe的储存设备  
> /dev/nvme 指NVMe固态硬盘

在输出的`Disk model`中可以看到磁盘的名称，用来判断你是否是你需要的磁盘。  
> Linux三分区法：将一个磁盘分成三个分区  
> 第一个为efi分区，用于Linux的启动引导  
> 第二个为根目录分区(/)，用来存放包管理器安装的软件  
> 第三个为家分区(/home)，用来存放用户个人数据  
> 如果硬盘空间足够大的话，建议给efi分区1Gb的空间，根目录150Gb的空间，剩下的给家目录  
> 如果购买的硬盘空间不够，或者是用原本的硬盘划过来的分区，则efi分区200Mb足够，根目录尽量保证70~100Gb的空间，剩下的再给家目录。  
> 建议在操作前先规划好分区，记在纸上，一会操作方便

当规划完分区后，输入命令`fdisk /dev/<yourdisk>`进入fdisk命令行。<yourdisk>指的是刚刚查出来的你的磁盘名  
fdisk命令行大概长这样：`Command (m for help):`  
**注意：在使用`w`命令之前，所有的操作都不会被写入磁盘，但一旦执行了`w`命令，就基本无力回天了，三思而行！**  
创建GPT分区表：`Command (m for help): g`  
*注：MBR分区表过于古老，不再使用*
> 两个概念：分区表和文件系统  
> 分区表：一个在磁盘最前面的表，记录了这个磁盘被分成了几块。  
> 文件系统：某个分区(由分区表记录)上记录文件的“格式”  
> 以Windows磁盘分区为例，分为两个步骤：  
> 第一步：在分区表里记录下磁盘这个位置(通常由用户、分区软件指定)，现在是一个独立分区  
> 第二步：按照文件系统的“格式”，对新建的分区进行初始化(也就是常见到的格式化)。

创建新分区：`Command (m for help): n`  
会提示你输入这个分区的序号(单纯就是序号，可以根据自己的喜欢给上述三个分区设定特定的序号)；设定完序号后会提示输入磁盘的首扇区，我没有这方面知识，默认走天下，~~反正是首扇区~~；设定完首扇区后会要求输入尾扇区，这里猜测首尾扇区决定了该分区大小，但fdisk允许你在这里直接输入分区的大小，如`+1G`。  
当三个分区都创建完毕后(tips：最后一个分区的尾扇区可以直接使用默认值，也就是最大扇区，表示将剩余空间全部分配给该分区)，需要更改分区的类型。  
查看目前三个分区的类型：`Command (m for help): p`  
目标为把efi分区的类型改为“EFI System”，根目录和家目录的类型为“Linux filesystem”。  
查看分区类型编号：`Command (m for help): l`  
输出中会给出各种类型编号，找到需要的类型，记录下编号，以“EFI System”的编号为`1`举例：  
更改分区类型：`Command (m for help): t`  
然后会要求输入分区序号，这个为之前个人设定，然后输入`1`将类型更改为“EFI System”。  
以上操作确认完没问题后，`Command (m for help): w`保存。**一定三思！**  
重新`fdisk -l`可以查看到，现在你的新磁盘已经多了三个分区了，并且也有了新名字  
然后格式化分区：`mkfs.fat -F32 /dev/<efi_partition>`(注意加上分区号)，这个命令用于`efi`分区，然后这个命令`mkfs.ext4 /dev/<rootandhome_partition>`用于根目录和家目录，~~不要用错，否则无力回天~~  
## 挂载分区
我们目前其实处于U盘的Linux系统下，如果要对分好区、格式化完毕的新磁盘进行操作，就需要将这些分区挂载到U盘上，就要用到mount命令。  
在U盘上创建两个文件夹：  
`mkdir -p /mnt/boot`以及`mkdir -p /mnt/home`，然后执行：  
```
mount /dev/efi_partition /mnt/boot
mount /dev/root_partition /mnt
mount /dev/home_partition /mnt/home
```
将各分区挂载到相应的文件夹。
## 安装ArchLinux
> 网上教程中会让你修改`/etc/pacman.d/mirrorlist`这个文件，但事实上，如果使用这个方法安装ArchLinux，刚连上网络时，pacman就已经自己更新了20个最快的源，因此不需要对这个文件做任何操作。  

使用该命令安装最新软件包：`pacstrap /mnt base linux linux-firmware`  
因为是在线安装系统，所以会需要一点时间，等待结束后，ArchLinux就已经安装到你的新硬盘中了。但是先别急着重启，首先在U盘下对新系统进行初步配置  
## 配置系统
### 转移到新系统
首先要让系统启动时把分区挂在上来，通过生成fstab文件实现  
命令：`genfstab -U /mnt >> /mnt/etc/fstab`  
然后Change root进入到新安装的系统：  
`# arch-chroot /mnt`  
执行完上面的命令后，你就在新的系统里了~~神奇~~  
### 时区与地区设置
设置时区(在中国，使用以下命令来设置时区)：  
`# ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime`  
然后`# hwclock --systohc`  
由于在新系统中，没有安装vim，因此需要使用pacman进行安装：
`# pacman -S vim`  
然后我们需要编辑地区：  
`# vim /etc/locale.gen`   
这里个人配置为将`en_US.UTF-8`和`zh_CN.UTF-8`取消注释  
然后生成locale信息：
`# locale-gen`  
创建`locale.conf`文件并编辑`LANG`变量：  
`# vim /etc/locale.conf`  
在新文件中添加一行`LANG=en_US.UTF-8`  
> 对于中文用户，有如下tips：  
> * 用户可以设置自己的locale，详情参阅[Wiki内容](https://wiki.archlinux.org/title/Locale_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#%E8%AE%BE%E7%BD%AE_locale)  
> * 将系统`locale`设置为`en_US.UTF-8`，系统的log就会用英文显示，这样更容易判断和处理问题
> * 也可以设置为`en_GB.UTF-8`或`en_SG.UTF-8`，优点如下：
>   * 进入桌面环境后以24小时制显示时间
>   * LibreOffice等办公软件的纸张尺寸会默认为A4而非Letter(US)
>   * 可尽量避免不必要且可能造成处理麻烦的英制单位  
> 
> 事实上这些优点对于编程开发几乎无用，大部分需要这些特性的情况下建议使用Windows，而本博客也正是建立在Windows和ArchLinux双系统的大前提下的。  
> **警告**: 不推荐在此设置任何中文 locale，会导致 tty 乱码。

### 网络配置
创建文件：  
`# vim /etc/hostname`  
`myhostname`(写上自己喜欢的名字即可，为本机名)  
`# vim /etc/hosts`并填入以下内容：  
```
127.0.0.1 localhost
::1 localhost
127.0.1.1 myhostname.localdomain myhostname
```
其中的`myhostname`改为自己设置的。  
### 设置root密码
`# passwd`  
按照引导输入密码，不会有回显  
### 安装引导程序
#### 安装双系统引导
使用`grub2`进行双系统引导  
安装os-prober：  
`# pacman -S os-prober`  
然后安装grub：  
`# grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB`  
*注：`/boot`这个位置，若之前挂载时创建文件夹使用了别的名字来做efi的挂载目录，则替换为正确的目录名*  
#### 挂载Windows启动分区
`# fdisk -l`找到Windows的efi分区，记下设备号  
在`/mnt`下新建一个目录，将Windows的efi分区挂载上去,示例如下：  
`mkdir -p /mnt/winefi`  
`mount /dev/<yourwinefi_partition> /mnt/winefi`  
#### 配置引导程序
编辑`/etc/default/grub`文件：  
`vim /etc/default/grub`  
在底下加入如下内容：  
```
#enable osprober
GRUB_DISABLE_OS_PROBER=false
```
然后执行：  
`# grub-mkconfig -o /boot/grub/grub.cfg`
查看一下`grub.cfg`文件：  
`#vim /boot/grub/grub.cfg`  
应该能找到如下内容：  
```
### BEGIN /etc/grub.d/30_os-prober ###
menuentry 'Windows Boot Manager (on ***)'
```
这样的内容，说明成功了  
### 创建普通用户
通过  
`# useradd -m -G wheel username`  
`# passwd username`  
两个命令来创建新用户并设置密码，避免使用root登录新系统  
> `-m`命令会在创建用户同时在`/home`目录下创建一个与用户名同名的文件夹  
> `-G`代表把用户加入一个组，wheel组拥有root的权限，方便sudo
### 安装网络管理软件
~~新系统中没有网络管理软件，如果此时重启进入新系统就会因为没有网络什么也做不了~~  
`# pacman -S NetworkManager`  
`# pacman -S dhcpcd`  
### 安装sudo
`# pacman -S sudo`
## 尾声
至此，准备工作完成，使用命令`exit`退出chroot，然后使用命令`poweroff`关机或`reboot`重启  
拔掉U盘，进入BIOS，把grub的引导顺序调到最前，保存并重启，就可以进入grub的引导界面，选择ArchLinux，进入新系统。