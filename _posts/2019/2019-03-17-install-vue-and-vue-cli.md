---
layout: post
title: Vue学习（一）：Vue的安装及使用Vue Cli快速生成新项目的脚手架
date: 2019-03-17 00:00:00
categories: 
- Vue-前端框架
tags: 
- Vue
- Vue-cli
- Javascript
description: Vue是一套用于构建用户界面的渐进式框架，是只针对前端的框架，它能够帮助我们快速地构造前端页面。利用Vue框架和其他后端框架比如express，能迅速构建单页应用。
---





# 前言  {#qianyan}
Vue是一套用于构建用户界面的渐进式框架，是只针对前端的框架，它能够帮助我们快速地构造前端页面。利用Vue框架和其他后端框架比如express，能迅速构建单页应用。详细信息请查看Vue的官方文档[https://cn.vuejs.org/v2/guide/](https://cn.vuejs.org/v2/guide/)

# Vue的安装  {#vue-install}
Vue的安装方法有很多，我是用的方法是直接用`<script>`标签引用。

首先前往Vue的github仓库[https://github.com/vuejs/vue](https://github.com/vuejs/vue)，把整个代码仓库下载到本地，然后只需要在项目中引用`dist`文件夹下的`vue.js`即可。

更多的安装方法详看官方文档[https://cn.vuejs.org/v2/guide/installation.html](https://cn.vuejs.org/v2/guide/installation.html)

# Vue Cli的安装  {#vue-cli-install}
使用Vue Cli快速生成新项目的脚手架，目前的版本为3.x，官方文档为[https://cli.vuejs.org/zh/guide/](https://cli.vuejs.org/zh/guide/)。Vue Cli新版与旧版的用法有些不同，为了紧跟潮流，这里使用3.x的版本，并且要求Node.js版本至少为8.9。

安装Vue Cli之前，强烈建议安装淘宝的cnpm，用cnpm代替npm进行各种依赖包的安装，不仅速度更快，而且能避免很多安装时的错误。我在用npm安装Vue Cli时，就遇到了不少错误，使用了cnpm后，就没问题了。要安装cnpm，就在命令行中输入并执行`npm install -g cnpm --registry=https://registry.npm.taobao.org`。

如果之前安装过旧版的Vue Cli，必须先执行命令`npm uninstall vue-cli -g`卸载。Vue Cli安装过程比较简单，只需要在命令行中输入并执行`cnpm install -g @vue/cli`即可。

# Vue Cli的使用  {#use}
首先在命令行中输入并执行`vue create 文件夹名`，比如我输入了`vue create vue-client`，结果如下图：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-17-1.png)

在这里，不要选择默认选项，而是选择`Manually select features`，并且把`Router`和`vuex`选上，这两个插件能帮助我们快速地开发项目。结果如下图：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-17-2.png)

接下来，关于Router，默认是hash模式，这里我们使用history模式。**注意，使用history模式需要对后端代码进行部分修改，才能使得前端页面不会出现404问题**，修改方法如下[https://router.vuejs.org/zh/guide/essentials/history-mode.html](https://router.vuejs.org/zh/guide/essentials/history-mode.html)。**如果自己的项目是纯前端项目，或自己无法修改服务端代码，则必须选默认hash模式**。如下图所示：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-17-3.png)


至于后面的一些选项根据自己实际情况选择即可，最后一个项目就创建好了，项目结构如下：
```
|-- vue-client
    |-- .browserslistrc
    |-- .editorconfig
    |-- .eslintrc.js
    |-- .gitignore
    |-- babel.config.js
    |-- package-lock.json
    |-- package.json
    |-- postcss.config.js
    |-- README.md
    |-- public
    |   |-- favicon.ico
    |   |-- index.html
    |-- src
        |-- App.vue
        |-- main.js
        |-- router.js
        |-- store.js
        |-- assets
        |   |-- logo.png
        |-- components
        |   |-- HelloWorld.vue
        |-- views
            |-- About.vue
            |-- Home.vue

```

进入刚创建好的文件夹，执行`npm run serve`，然后再浏览器输入`http://localhost:8080/`即可看到页面内容。

使用Vue Cli创建web前端页面后，就不需要另外引入vue.js库文件了，因为Vue Cli自动为我们引入了所需的库。
