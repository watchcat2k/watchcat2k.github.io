---
layout: post
title: Ubuntu 16.04环境下mongodb的安装与服务的开启
date: 2019-03-29 10:00:00
categories: 
- Server-服务器相关
tags: 
- Server
- Ubuntu 
- Mongodb
description: Ubuntu 16.04环境下mongodb的安装与服务的开启。
---


# 下载mongodb  {#download}
前往mongodb的官网[https://www.mongodb.com/download-center/community](https://www.mongodb.com/download-center/community)选择下载Ubuntu 16.04版本的mongodb。如下图：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-29-1.png)

下载完成后，会得到一个后缀名为deb的软件包，如下图：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-29-2.png)

# 安装mongodb  {#install}
安装后缀名为deb的文件，需要用命令行来安装。

在mongodb的deb软件包那一层目录打开命令行，输入以下命令即可：
```
sudo dpkg -i mongodb-org-server_4.0.7_amd64.deb
```
其中xxx.deb是对应的文件名。

完成安装后，ubuntu系统自动地配置好mongodb环境，我们可以直接使用mongod命令，于是在命令行中输入
```
mongod --version
```
查看mongod的版本以确定是否完成安装，如下图：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-29-3.png)

# 开启mongodb服务  {#open}
前面提到，使用这种方法安装mongodb，ubuntu系统自动地配置好mongodb环境，我们可以直接使用mongod命令。所以要开启mongodb服务，只需输入以下命令：
```
mongod --dbpath c:\data
```
那么就会在c:\data目录下生成数据库文件，并在默认端口27017下开启数据库服务。c:\data可以替换成其它路径，开启服务的端口也可以通过命令来改变
```
mongod --port 37017 --dbpath c:\data
```
这样的话mongodb服务就会在37017开启，在后端程序代码中使用该端口连接就可操作数据库。


