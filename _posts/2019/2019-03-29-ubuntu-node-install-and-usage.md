---
layout: post
title: Ubuntu 16.04环境下nodejs的安装和配置
date: 2019-03-29 12:00:00
categories: 
- Server-服务器相关
tags: 
- Server
- Ubuntu
- Node
description: Ubuntu 16.04环境下nodejs的安装和配置。
---


# 下载nodejs  {#download}
在ubuntu环境下，前往nodejs官网[https://nodejs.org/en/](https://nodejs.org/en/)，nodejs官网能自动检测自己的系统版本，推荐出合适的nodejs版本，如下图：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-29-4.png)

下载之后，得到一个后缀名为tar.xz的压缩包。把压缩包解压，得到一个名为`node-v10.15.3-linux-x64`的文件夹，把这个文件夹剪切或复制到/opt/目录下，如下图：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-29-5.png)

# 配置nodejs  {#setting}
找到/etc/目录下的bash.bashrc文件，如下图：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-29-6.png)

然后修改其中的内容，在文件末尾加上以下代码，然后保存即可：
```
export NODE_HOME=/opt/node-v10.15.3-linux-x64
export PATH=$NODE_HOME/bin:$PATH
```
如下图所示：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-29-7.png)

最后在命令行中以下命令查看node版本，验证是否成功安装
```
node -v
```


