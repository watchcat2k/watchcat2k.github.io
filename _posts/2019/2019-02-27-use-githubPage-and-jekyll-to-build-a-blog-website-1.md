---
layout: post
title: 利用 github page 和 jekyll 搭建个人博客网站（一）
date: 2019-02-27 00:00:00
categories: 
- Github-Page博客网站建设
tags: 
- Github-Page
- Jekyll
- LessOrMore
description: 利用 github 提供的 github page 服务，搭建一个完全免费的属于自己的博客网站。
---


本人根据这篇博客[https://blog.csdn.net/KNIGH_YUN/article/details/79774344](https://blog.csdn.net/KNIGH_YUN/article/details/79774344)所描述的步骤搭建了属于自己的博客网站。现在，在这里对博客搭建的过程进行复述和总结。

# 前言							{#qianyuan}
说到搭建博客网站，有些同学可能会想到要首先要搭建一个服务器，还要购买域名，还要写前后端代码等等，不仅过程繁琐，而且要花费一定金钱。然而，利用一些工具，可以搭建一个永久的能访问的博客网站（*这里指的是静态的博客网站*）并不是一件困难的事，并且还不用担心服务器、域名等问题。这些工具就是**github page**和**jekyll**。


# 使用github page							{#use-github-page}

## 注册github账号							{#regist-github-page-account}

首先前往[github官网](https://github.com/)注册账号，这个步骤比较简单。

## 新建一个仓库							{#new-a-repository}

在个人主页上，点击左侧栏的`New`按钮，如下图所示

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-02/2019-02-27-1.png)

进入新建页面，将仓库的`repository name`设置为`username.github.io`，其中`username`替换为自己的用户名，如下图所示。之所以这样子设置，是因为github只向每个人提供了一个github page，而要使用这个github page，就将自己的某个仓库的`repository name`设置成`username.github.io`的形式，这样该仓库里的内容就可以变成成网页内容，进入`https://username.github.io`就可以查看。

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-02/2019-02-27-2.png)

## 利用git工具克隆到本地							{#use-git-clone}

在前面的步骤中，我们创建了一个仓库，并配置为github page，但里面还什么内容都没有，我们需要利用git工具的`git clone`命令将该仓库克隆到本地文件夹，然后在文件夹里添加内容，再提交到github的个人仓库上。git工具的使用请参考这篇博客[https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)


# 使用Jekyll							{#use-jekyll}

## jekyll的下载安装							{#download-jekyll}

1. **安装ruby**：因为jekyll依赖于ruby，所以必须先安装ruby，前往[https://www.ruby-lang.org/en/downloads/](https://www.ruby-lang.org/en/downloads/)根据自己的系统下载安装ruby。
   
2. **安装gem**：安装了ruby之后，默认会安装gem，可以在命令行里测试是否已经安装成功，如下图。如果没有安装gem，可以前往此处[https://rubygems.org/pages/download](https://rubygems.org/pages/download)下载安装

    ![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-02/2019-02-27-3.png)

3. **安装jekyll**：在命令行中输入`gem install jekyll`，jekyll安装完成后，还不能使用，还要安装一些组件。此时在命令行中输入`gem install bundler`，安装完成后，再在命令行中输入`bundle install`。这些步骤做完之后，jekyll就可以使用了。

## jekyll的使用							{#how-to-use-jekyll}

关于Jekyll的使用，可以前往[Jekyll官网](https://www.jekyll.com.cn/)查看，这里给出简单的使用方法。

1. 利用命令行进入一个空文件夹，然后执行`jekyll new my-first-website`，然后jekyll就生成一个`my-first-website`文件夹，里面的内容是这样的：

    ![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-02/2019-02-27-4.png)

2. 在命令行中进入该文件夹，执行`jekyll server`，然后再浏览器中打开地址`http://127.0.0.1:4000/`，就可以看到网页的内容
   ![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-02/2019-02-27-5.png)


# 更换jekyll主题							{#change-jekyll-theme}

## 寻找jekyll主题							{#find-jekyll-theme}

前面可以看到，Jekyll默认生成的网页主题单调，我们可以换一个更好的主题。前往[jekyll theme](http://jekyllthemes.org/)网站寻找自己喜欢的主题，我是用的主题是[Less-Or-More](http://jekyllthemes.org/themes/Less-Or-More/)。点击下图中的`dowload`按钮即可下载。

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-02/2019-02-27-6.png)

## 使用jekyll主题							{#use-jekyll-theme}

将刚刚下载好的主题压缩包解压，然后在命令行中进入`LessOrMore-master`文件夹，执行`jekyll server`命令，然后再浏览器中打开地址`http://127.0.0.1:4000/LessOrMore/`，即可看到网站内容。那么，使用了这个主题之后，之前利用`jekyll new my-first-website`生成的文件夹及里面的内容就可以删掉不要了。

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-02/2019-02-27-7.png)


# 将本地网站内容部署到github上							{#upload-local-content}

1. **复制文件**：之前的步骤我们利用git工具将github上一个名为`username.github.io`的空仓库克隆到了本地文件夹，那么我们将`LessOrMore`主题文件夹里面的全部文件复制到那个本地文件夹里。（注意，是复制`LessOrMore`主题文件夹里面的全部文件，而不是`LessOrMore`主题文件夹），即以下文件
   
    ![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-02/2019-02-27-8.png)

2. **修改_config.yml文件**：该主题的`README.md`文件里面有些一些注意事项，特别注意baseurl的配置，如果是`***.github.io`项目，不修改为空""的话，会导致JS,CSS等静态资源无法找到的错误。所以我们要做一个修改，把`_config.yml`文件里的baseurl属性值修改为空，如下图所示：
   
    ![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-02/2019-02-27-9.png)

3. **提交到github**：利用git工具执行`git add -A`和`git commit -m "xxx"`命令，然后再执行`git push`命令，就能将这些文件提交到自己的github账号上，如下图
   
    ![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-02/2019-02-27-10.png)

4. **浏览器查看网站**：在浏览器中输入`https://username.github.io/`，把username修改为自己的github用户名，即可查看网站内容。


# 如何更新博客							{#how-to-update-blog}
这一点在`LessOrMore`的`README.md`文件里面有提到，我把那段文字复制下来。

在`LessOrMore/_posts`目录下新建一个文件，可以创建文件夹并在文件夹中添加文件，方便维护。在新建文件中粘贴如下信息，并修改以下的`titile`,`date`,`categories`,`tag`的相关信息，添加`* content {:toc}`为目录相关信息，在进行正文书写前需要在目录和正文之间输入至少2行空行。然后按照正常的Markdown语法书写正文。

```bash
---
layout: post
#标题配置
title:  标题
#时间配置
date:   2016-08-27 01:08:00 +0800
#大类配置
categories: document
#小类配置
tag: 教程
---

* content
{:toc}


我是正文。我是正文。我是正文。我是正文。我是正文。我是正文。
```

注意写博客时，要给每个带有`#`的标题打上标签`{#xxx}`，否则网页上的目录显示错误。

# 博客图片引用  {#image-link}
关于博客中图片的引用，我一开始是将图片上传到CSDN，然后在博客文章中使用CSDN提供的链接，后来发现这种方法不太行，图片经常加载不出来，可能是CSDN屏蔽了外部对它网站博客图片的引用。

这里我使用github来存储博客要使用的图片，我直接在本项目的`styles/images`目录下，存储博客中使用的图片，然后两种方法引用图片：
1. 一种是在博客内直接使用相对路径引用图片，比如`../styles/images/xxx.jpg`。
2. 第二种是先把图片commit到github上，利用github生成的图片链接进行引用。这种方法方便博客文章能顺利在不同的平台发布，不会因为使用图片相对路径而找不到图片。
   
要获取图片的github链接，就在github上找到该图片，如下图：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-02/2019-02-27-11.png)

然后点击`download`按钮，得到的网址便是可以引用的图片网址，如下图:

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-02/2019-02-27-12.png)