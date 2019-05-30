---
layout: post
title: koa2开发Web应用：利用koa-generator快速生成koa2框架目录结构
date: 2019-03-16 00:00:00
categories: 
- Koa-Web框架
tags: 
- Koa
- Node
- Koa-generator
description: koa是一个基于Node.js平台的新一代Web应用开发框架，利用 koa-generator 能快速生成koa2框架目录结构。
---



# 前言  {#qianyan}
koa是一个基于Node.js平台的新一代Web应用开发框架，由Express 幕后的原班人马打造，目前最新版本是koa2。与Express不同的是，koa通过利用 async 函数，丢弃了复杂的回调函数，并有力地增强错误处理。这里有koa框架中文学习文档[https://koa.bootcss.com/#](https://koa.bootcss.com/#)。

# 安装koa-generator  {#install}
在命令行中输入并运行`npm install koa-generator -g`
注意，koa框架要求node版本至少为 v7.6.0

# 创建koa框架目录结构  {#create}
 进入一个空文件夹，然后在命令行中输入并运行`koa2 koa-server`，那么就创建了koa-server文件夹，里面的文件便是koa框架问价，koa2框架目录结构如下：
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

# 安装依赖项  {#install-dependency}
进入koa-server文件夹，在命令行输入并执行`npm install`。安装完成后，目录中多了一个node_modules文件夹和package-lock.json文件，node_modules文件夹的内容不用git工具进行commit，package-lock.json文件及其它文件都需要commit。

**（以下问题若没有出现可直接忽略）**

我这里在安装依赖项时，出现了警告和错误，如下图：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-16-1.png)

命令行里提示我们要执行`npm audit fix`修复问题，然而运行后还是不能解决问题，如下图：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-16-2.png)

我按照提示，执行`npm audit`，尝试手动解决，如下图：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-16-3.png)

命令行提示我输入并执行`npm install koa-onerror@4.1.0`，完成后，项目终于没有问题了，如下图：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-16-4.png)

# 运行Web应用  {#run}
在koa-server文件夹下，按照安装koa框架时的提示，先执行`SET DEBUG=koa*`，然后再执行`npm start koa-server`或`node bin/www`，之后在浏览器中访问`http://localhost:3000/`，即可看到koa框架渲染的页面。

