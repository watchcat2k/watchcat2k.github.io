---
layout: post
title: Markdown语法：利用mddir工具自动生成文件目录结构
date: 2019-03-15 00:00:00
categories: 
- Markdown
tags: 
- Markdown
- Mddir
description: mddir是个能帮助我们快速生成markdown语法形式的文件目录结构的工具。
---




# 前言  {#qianyan}
mddir是个能帮助我们快速生成markdown语法形式的文件目录结构的工具，详细说明请参考官方文档[https://www.npmjs.com/package/mddir](https://www.npmjs.com/package/mddir)

# 安装mddir工具  {#install}
要安装mddir工具，必须先安装npm包管理工具。我的npm包管理工具是在安装node时自动安装的，node的下载安装地址如下：[https://nodejs.org/zh-cn/download/](https://nodejs.org/zh-cn/download/)

在命令行中输入并执行`npm install mddir -g`便可完成安装

# 使用mddir工具  {#use}
假设现在有一个文件夹koa-server，里面的文件目录结构如下图所示：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-16-5.png)

要使用mddir工具，我们首先利用命令行进入koa-server文件夹，然后输入并执行`mddir`命令，最终在koa-server文件夹内会生成一个directoryList.md文件，如下图所示：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-16-6.png)

mddir工具默认会忽略node_modules和.git文件夹。而directoryList.md文件内容如下图所示：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-16-7.png)

第一行出现的'undefined'用父文件夹名，即koa-server代替即可。注意，上图内容还是纯文本内容，markdown解析器解析渲染之后并不会正确显示，而是需要像markdown中的代码块一样用两个三重反引号(```)包围起来，最终结果如下所示：
```
|-- koa-server
    |-- app.js
    |-- package-lock.json
    |-- package.json
    |-- bin
    |   |-- www
    |-- public
    |   |-- images
    |   |-- javascripts
    |   |-- stylesheets
    |       |-- style.css
    |-- routes
    |   |-- index.js
    |   |-- users.js
    |-- views
        |-- error.pug
        |-- index.pug
        |-- layout.pug

```



