---
layout: post
title: 如何将web项目部署到VPS服务器上
date: 2019-03-29 15:00:00
categories: 
- Server-服务器相关
tags: 
- Server
- VPS
- Node
- Mongodb
description: 我之前已经搭建好了自己的VPS服务器，并写好了一个博客网站系统的小项目并上传到github上，现在讲解如何把web项目部署到VPS服务器上。
---


# 项目文件的下载  {#download}
根据我之前的博客[https://blog.csdn.net/qq_36272282/article/details/88234671](https://blog.csdn.net/qq_36272282/article/details/88234671)，已经搭建好了自己的VPS服务器，然后通过远程桌面连接到服务器。我之前已经写好了一个博客网站系统的小项目并上传到github上[https://github.com/watchcat2k/my_blog_expresstest](https://github.com/watchcat2k/my_blog_expresstest)，这时，在服务器中，下载该项目到服务器硬盘目录中，如下图：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-29-8.png)

我的项目用到了node和mongodb，所以要现在服务器中配置好node和mongodb，node的配置参考[https://blog.csdn.net/qq_36272282/article/details/88887360](https://blog.csdn.net/qq_36272282/article/details/88887360)，mongodb的配置参考[https://blog.csdn.net/qq_36272282/article/details/88885509](https://blog.csdn.net/qq_36272282/article/details/88885509)

在项目目录中打开命令行，我们还需要为项目安装模块文件。
首先，强烈推荐使用npm的淘宝镜像cnpm进行各种模块的安装，要安装cnpm，就在命令行中执行：
```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

然后使用cnpm安装项目依赖模块，在命令行中执行以下命令：
```
cnpm install
```

# 项目服务开启  {#open}
首先要开启mongodb数据库服务。我在项目文件的上一层目录建立了一个空文件夹`data`，如下图：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-29-9.png)

然后，在该层目录下打开命令行，使用mongod命令在data文件夹中生成数据库文件并开启数据库服务：
```
mongod --dbpath data 
```
保持该命令行的开启。进入项目文件目录，打开新的命令行窗口，开启网站后端服务：
```
node bin/www
```
也保持这个窗口开启。还有，命令行的输出提示我们网站服务在3000端口开启，我们可以在服务器的浏览器上输入`localhost:3000`即可查看网站。

# VPS服务器防火墙设置  {#setting}
完成上面的步骤，我们的网站项目可以在服务器内部访问，但还不能被外部网络访问。还需要配置VPS服务器防火墙（出入组规则）。

我使用的是阿里云的VPS轻量应用服务器，在阿里云打开服务器控制台，进入防火墙配置，点击“添加规则”，端口为网站项目服务的开启端口，如下图中红线圈起来的规则：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-29-10.png)

完成之后，在外部网络浏览器中，输入`xxx.xxx.xxx.xxx:3000`即可访问该网站，其中`xxx.xxx.xxx.xxx:`是服务器的公网IP，3000是该网站项目服务的开启端口。

