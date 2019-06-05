---
layout: post
title: 利用github page和jekyll搭建个人博客网站（三）：使用最热门的 Next 主题并进行深度配置
date: 2019-06-05 15:20:00
categories: 
- Github-Page博客网站建设
tags: 
- Github-Page
- Jekyll
- Jekyll-Next-Theme
description: 在之前的教程中我们利用 github 提供的 github page 服务，已经搭建了一个属于自己的博客网站。但是，我对这个 jekyll 搭建起来的个人博客网站并不是很满意，一个是我对这个 jekyll 主题并不是很中意，第二个是这个主题的功能不是很丰富，所以我决定更换主题。恰好，我找到了一个非常热门并且功能丰富自己又中意的主题：Jekyll-Next-Theme。
---



# 前言
自己一直想做一个个人专属的博客网站，所以之前使用 [jekyll](http://jekyllcn.com/)  并配合 github page 搭建了个人的博客网站，详情参考我的博客[https://blog.csdn.net/qq_36272282/article/details/87987068](https://blog.csdn.net/qq_36272282/article/details/87987068)。

但是，我对这个 jekyll 搭建起来的个人博客网站并不是很满意，一个是我对这个 jekyll 主题并不是很中意，自己找了很久都找不到中意的 jekyll 主题，第二个是这个主题的功能不是很丰富，没有一些自己想要的功能，所以我决定更换主题。恰好，我找到了一个非常热门并且功能丰富自己又中意的主题：[Jekyll-Next-Theme](http://theme-next.simpleyyt.com/getting-started.html)，github 地址为 [https://github.com/Simpleyyt/jekyll-theme-next](https://github.com/Simpleyyt/jekyll-theme-next)。

本文只总结一些配置过程中容易出错的关键点，主要配置教程参考[官方文档](http://theme-next.simpleyyt.com/getting-started.html)。

# 安装 NexT
首先确保已安装`Ruby 2.1.0` 或更高版本：
```
ruby --version
```
然后安装 Bundler：
```
gem install bundler
```
下载 NexT 主题到本地：
```
git clone https://github.com/Simpleyyt/jekyll-theme-next.git
cd jekyll-theme-next
```
以下是与官方教程不一样的地方。
首先查看 Gemfile.lock 文件末尾，如下：
```
PLATFORMS
  ruby
  x64-mingw32

DEPENDENCIES
  github-pages

BUNDLED WITH
   1.17.1
```
BUNDLED WITH 指明了要求 bundler 版本为 1.17.1，所以我们应该先安装对应版本的 bundler：
```
gem install bundler -v '1.17.1'
```
然后安装依赖：
```
bundle install
```
然后，我们要先删除本项目的隐藏文件 .git 文件，然后再自己的 github 账户新建一个名为 `jekyll-theme-next` 的仓库（名字自取，如果配合 github page，就取名为 `xxx.github.io`），然后把本项目 push 到自己 github 下的新建的仓库，然后在 _config.yml 文件末尾添加以下代码：
```
# 仓库名
repository: watchcat2k/jekyll-theme-next
```
这就完成了安装，以命令运行即可在 `http://127.0.0.1:4000` 处访问：
```
bundle exec jekyll server
```
运行后，命令行出现了一些提示，如下图：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-05/2019-05-27-1.png)

根据命令行输出提示，我们把下列代码添加到`Gemfile`
```
gem 'wdm', '>= 0.1.0' if Gem.win_platform?
```
然后要注意的是，运行服务的命令不要添加 `--incremental` 参数，不然无法预览新的 post：

# 效果
最终运行结果图如下：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-05/2019-05-27-2.png)

# 深度配置
该主题的配置详情参考[官方文档](http://theme-next.simpleyyt.com/getting-started.html)，这里只对一些关键点进行说明。

## 编码问题
我在 window 本地环境下运行 jekyll 服务器进行网站的预览时，有出现博客无法找到、无法打开预览的情况，原因是这些博客的文件名、分类名或标签名使用了中文，jekyll 无法正常解析。要解决这个问题，就要找到 window 环境下的 ruby 安装路径，如下：
```
C:\Ruby25-x64\lib\ruby\2.5.0\webrick\httpservlet
```
找到该路径下的 `filehandler.rb` 文件，修改下面两处地方（有+的一行为添加部分）：
```
path = req.path_info.dup.force_encoding(Encoding.find("filesystem"))
+ path.force_encoding("UTF-8") # 加入编码
if trailing_pathsep?(req.path_info)
```
```
break if base == "/"
+ base.force_encoding("UTF-8") #加入編碼
break unless File.directory?(File.expand_path(res.filename + base))
```

## 开启打赏功能
Jekyll-Next-Theme 主题的 _config.yml 配置文件默认是没有关于打赏功能的字段的，我们自己添加相应字段后便可开启打赏功能。所以在配置文件添加以下代码：
```
reward_comment: 坚持原创技术分享，您的支持将鼓励我继续创作！
wechatpay: /path/to/wechat-reward-image
alipay: /path/to/alipay-reward-image
```

## 修改页面宽度
我使用的主题是 `scheme: Pisces`，这个主题有个问题就是页面太窄了，在浏览器上看文章和代码比较吃力，所以推荐修改一下页面宽度。

首先找到 `Pisces` 的布局文件，它的路径为 `xxx.github.io/_sass/_schemes/Pisces/_layout.scss` ，然后修改一下三处地方（有注释的地方就是要修改的地方）：
```
.header {
  position: relative;
  margin: 0 auto;
  //width: $main-desktop;
  width: 80%;

  @include tablet() {
    width: auto;
  }
  @include mobile() {
    width: auto;
  }
}

.container .main-inner {
  //width: $main-desktop;
  width: 80%;

  @include tablet() {
    width: auto;
  }
  @include mobile() {
    width: auto;
  }
}

.content-wrap {
  float: right;
  box-sizing: border-box;
  padding: $content-desktop-padding;
  //width: $content-desktop;
  width: calc(100% - 260px);

  background: white;
  min-height: 700px;
  box-shadow: $box-shadow-inner;
  border-radius: $border-radius-inner;

  @include tablet() {
    width: 100%;
    padding: 20px;
    border-radius: initial;
  }
  @include mobile() {
    width: 100%;
    padding: 20px;
    min-height: auto;
    border-radius: initial;
  }
}
```
至于其它两个主题的页面宽度修改方式，参考官方文档[http://theme-next.simpleyyt.com/faqs.html#custom-content-width](http://theme-next.simpleyyt.com/faqs.html#custom-content-width)


