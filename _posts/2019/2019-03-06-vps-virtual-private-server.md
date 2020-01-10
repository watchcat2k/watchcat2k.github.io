---
layout: post
title: VPS 虚拟专用服务器的搭建和配置
date: 2019-03-06 00:00:00
categories: 
- Server-服务器相关
tags: 
- Server
- VPS
description: VPS，全称为Virtual Private Server，即虚拟专用服务器。这种技术是将一台服务器分割成多个虚拟专享服务器的优质服务，简单理解，VPS就是一台拥有公网IP的服务器
---


# 前言  {#qianyan}
VPS，全称为Virtual Private Server，即虚拟专用服务器。这种技术是将一台服务器分割成多个虚拟专享服务器的优质服务，简单理解，VPS就是一台拥有公网IP的服务器。

VPS虚拟专用服务器与ECS云服务器有些不同。VPS是利用虚拟技术在一台物理服务器上划分出来一部分内存、硬盘、带宽搭建而成的一个虚拟服务器；而云服务器是利用虚拟技术在一组集群服务器上划分出来的多个类似独立主机的部分。两者在功能、使用方法等方面是完全一样的。不同的是，VPS是在一台物理服务器上运行的，一旦这台物理服务器出现故障，上面所有的VPS都将受影响无法正常使用；而云服务器是在集群服务器中运行的，集群中的每台机器都会有云服务器的镜像备份。即使其中一台机器或者几台机器出现故障，依然不影响云服务器的正常访问。（资料参考自[https://zhidao.baidu.com/question/1865671620875563107.html](https://zhidao.baidu.com/question/1865671620875563107.html)）

# VPS的获取  {#get-vps}
本人的VPS服务器是在阿里云购买的[https://www.aliyun.com/product/swas?spm=5176.8142029.735711.9.79ca6d3ey2GI3I](https://www.aliyun.com/product/swas?spm=5176.8142029.735711.9.79ca6d3ey2GI3I)，根据自己的需要购买相应性能的VPS。购买时要选择服务器预装系统，我选择的是Ubuntu系统（服务器系统可以在购买后随时更换，所以不用担心）。如下图所示：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-06-1.png)

购买完成后，去到个人账户控制台，点击左侧栏的“产品与服务”，在“弹性计算”这一项中找到“轻量应用服务器”，点击即可找到自己的VPS。如下图所示：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-06-2.png)

# VPS的远程连接  {#vps-connect}
在个人VPS控制台的左侧栏中，点击“远程连接”，点击“使用浏览器发起安全连接”，即可连接到VPS服务器。如下图所示：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-06-3.png)

连接完成后，便弹出了命令行式的Ubuntu系统服务器。如下图所示：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-06-4.png)

# VPS图形界面  {#graph-desktop}
在进行了VPS的远程连接后，我们得到的是一个命令行式的操作系统服务器，这样子很不方便，我们希望得到的是一个图形界面的操作系统。图形界面的安装步骤如下：

## 设置VPS的用户密码  {#set-username-password}
在进行“远程连接”那个控制台界面，可以设置VPS的账号密码，如下图所示：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-06-5.png)

设置好密码后，使用我们的VPS服务器操作系统的用户名为`root`，密码为刚刚设置的密码。

## 安装图形界面  {#install}
### ubuntu 16.04
首先在“远程连接”控制台界面使用浏览器进行远程连接，进入命令行式系统，然后再命令行中输入并执行

```
sudo apt-get update
```

然后，在命令行中输入并执行

```
sudo apt-get install xrdp
```

xrdp是远程连接时控制图形界面的一个重要工具。

最后在命令行中输入并执行

```
sudo apt-get install xubuntu-desktop
```
这时的安装时间比较长。（**注意，xubuntu-desktop是仅限于ubuntu系统，如果VPS预装系统是其它的话，请自行查找对应的图形界面**）

### centos 7.3
centos 7.3图形界面安装教程参考自：[https://www.linuxidc.com/Linux/2018-04/152000.htm](https://www.linuxidc.com/Linux/2018-04/152000.htm)

远程连接centos教程参考自：[https://blog.csdn.net/txz317/article/details/51734222](https://blog.csdn.net/txz317/article/details/51734222)

首先切换到root账号：
```
sudo su root
```
然后安装X窗口系统，安装过程中都选择“yes”即可：
```
yum groupinstall "X Window System"
```
然后查看已安装的软件和已安装的软件：
```
yum grouplist
```

![在这里插入图片描述](https://gitee.com/watchcat2k/pictures_base/raw/master/2020/1/10-1.png)

根据查看的内容，选择对应名称的图形界面软件，这里安装GNOME图形界面：
```
yum groupinstall "GNOME Desktop" "Graphical Administration Tools"
```
然后，为了能后在win系统上远程连接centos，要安装xrdp：
```
yum install xrdp
```
再安装tigervnc：
```
yum install tigervnc tigervnc-server
```
配置SELinux , 否则可能无法启动xrdp服务,或者启动出错：
```
chcon -t bin_t /usr/sbin/xrdp
chcon -t bin_t /usr/sbin/xrdp-sesman
```
启动xrdp服务,并设置为开机启动：
```
systemctl start xrdp
systemctl enable xrdp
```


## 使用账号密码远程连接  {#start-connect}
安装完图形界面后，如果使用浏览器进行远程连接，得到的仍然是命令行式界面，为了能使图形界面生效，我们要使用账号密码方式进行远程连接。

在个人电脑里打开"远程桌面连接"，如下图所示：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-06-6.png)

输入个人VPS的公网IP地址，然后输入用户名，即“root”，密码为刚刚设置的密码，即可进入图形界面（一开始可能会黑屏，等待片刻即可），如下图所示：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-06-7.png)

