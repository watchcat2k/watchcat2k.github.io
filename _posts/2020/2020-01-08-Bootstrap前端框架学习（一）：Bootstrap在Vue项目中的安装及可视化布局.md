---
layout: post
title: Bootstrap前端框架学习（一）：Bootstrap在Vue项目中的安装及可视化布局
date: 2020-01-08 00:00:00
categories: 
- Bootstrap-前端框架
tags: 
- Vue
- Vue-cli
- Bootstrap
description: Bootstrap是简洁、直观、强悍的前端开发框架，可以让web开发更迅速、简单。这里主要介绍bootstrap在vue-cli创建的项目中的应用，及bootstrap的在线可视化工具。
---



# node.js的安装
我们需要安装node.js，使用node.js的npm包管理器来完成Bootstrap的安装。

前往[Node.js中文网](http://nodejs.cn/)下载并安装node.js，安装程序会自动把node添加到环境变量，在安装完成后重启即可生效。

重启后在终端中输入以下命令查看node.js是否安装成功：
```
node -v
```
然后输入以下命令查看npm是否安装成功：
```
npm -v
```
若能查看版本号即安装成功。

# bootstrap的安装
我选择bootstrap的最新版本bootstrap4进行安装，前往[bootstrap4的官方网站](https://v4.bootcss.com/)（或[bootstrap4中文站](https://code.z01.com/v4/)），参考官方教程进行下载安装。

**（注意，bootstrap中的js插件依赖于jquery，因此安装了bootstrap后，还要安装jquery并在bootstrap之前引用。并且bootstrap4中增加了对popper.js的依赖，因此，也要安装popper.js并在bootstrap之前引用。）**

在官方教程中给出了[HTML模板](https://code.z01.com/v4/docs/)，如下：
```
<!doctype html>
<html lang="en">
  <head>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">

    <title>Hello, world!</title>
  </head>
  <body>
    <h1>Hello, world!</h1>

    <!-- Optional JavaScript -->
    <!-- jQuery first, then Popper.js, then Bootstrap JS -->
    <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js" integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script>
  </body>
</html>
```
我们可以直接按照模板通过网络直接引用bootstrap及对应的依赖库，但这容易受网络波动影响，我选择用npm直接手动下载到本地。

我以vue-cli创建的vue项目为例，在vue项目根目录中执行以下语句：
```
npm install bootstrap@4.3.1
```
然后根据HTML模板，bootstrap-4.3.1对应jquery-3.3.1和popper-1.14.7，所以我们下载指定版本的依赖库：
```
npm install jquery@3.3.1
npm install popper.js@1.14.7
```
这样，bootstrap、jquery、popper包会被下载到`node_modules/`文件夹下，接下来便是在代码中引用上述包。

若是普通的HTML项目，则按照上述HTML模板的位置和顺序在HTML文件中引用即可。但是在vue-cli项目中，不能在`index.html`文件里引用，这样子引用是无效的，应该在`main.js`文件里通过import引用，如下：
```
import "jquery/dist/jquery.min";
import "popper.js/dist/popper.min";
import "bootstrap/dist/css/bootstrap.min.css";
import "bootstrap/dist/js/bootstrap.min";
```
注意bootstrap的引用放在最后。

# bootstrap快速布局
完成了bootstrap的安装与引用，接下来便是bootstrap的布局和实现。

我们可以直接用代码手动构建bootstrap布局页面，但这样比较麻烦，推荐使用在线可视化工具：
- 官方的[在线可视化布局工具Layoutit!](https://www.layoutit.com/)，支持bootstrap4
- 还有国内的[中文版在线可视化布局工具](https://www.runoob.com/try/bootstrap/layoutit/)，支持bootstrap3，不支持bootstrap4

利用在线工具可以迅速生成期待的布局，并下载对应的HTML代码，如下图：

![在这里插入图片描述](https://gitee.com/watchcat2k/pictures_base/raw/master/2020/1/8-1.png)




