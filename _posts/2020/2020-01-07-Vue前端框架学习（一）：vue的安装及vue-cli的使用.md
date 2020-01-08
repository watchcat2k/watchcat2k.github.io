---
layout: post
title: Vue前端框架学习（一）：vue的安装及vue-cli的使用
date: 2020-01-07 00:00:00
categories: 
- Vue-前端框架
tags: 
- Vue
- Vue-cli
- Javascript
description: Vue是一套用于构建用户界面的渐进式框架，是只针对前端的框架，它能够帮助我们快速地构造前端页面。利用Vue框架和其他后端框架比如express，能迅速构建单页应用。
---



# node.js的安装  
node.js是用来开发服务端的一门语言，我们学习vue前端框架并不需要学习node.js，但是我们需要安装node.js，使用node.js的npm包管理器来完成vue的环境搭建。

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

# cnpm的安装
npm是node.js的包管理器，用于node插件管理，但npm安装插件时从国外服务器下载，速度较慢，我们需要使用国内淘宝镜像cnpm代替国外服务器。

通过全局安装cnpm
```
npm install -g cnpm
```
查看cnpm是否安装成功
```
cnpm -v
```
若在power shell中出现如下错误：

![在这里插入图片描述](https://gitee.com/watchcat2k/pictures_base/raw/master/2020/1/1.png)

解决办法有两个[^1]：
1. 以管理员身份运行终端
2. 在终端中执行命令`set-ExecutionPolicy RemoteSigned`

然后再查看cnpm是否安装成功。

# vue-cli的安装
若要安装vue-cli2，则在终端中输入以下命令：
```
cnpm install -g vue-cli
```
若要安装vue-cli3，则先卸载vue-cli2，在安装vue-cli3：
```
npm uninstall vue-cli -g
cnpm install -g @vue/cli
```
然后使用以下命令查看是否安装成功：
```
vue -V
```
vue-cli有vue-cli2和vue-cli3两种版本，但vue-cli3有更多新特性，所以推荐安装vue-cli3。

# 创建vue-cli3项目  {#create-project}
## 以图形界面方式创建  {#ui}
vue-cli3增加了以图形界面来管理项目的方式，用一下命令打开图形界面：
```
vue ui
```
在浏览器中访问`http://localhost:8000`即可得以下画面：

![在这里插入图片描述](https://gitee.com/watchcat2k/pictures_base/raw/master/2020/1/2.png)

则可以通过操作图形界面创建、导入项目并对项目进行管理。

## 以命令行方式创建  {#cmd}
因为使用图形界面管理项目的速度比较慢，我们一般直接使用命令行的方式直接管理项目。

使用以下命令创建vue-cli项目
```
vue create projectName
```

创建过程中需要我们选定一些选项（空格键选择，回车键确认）：

![在这里插入图片描述](https://gitee.com/watchcat2k/pictures_base/raw/master/2020/1/3.png)

其中一些选项的含义为：
- Please pick a preset?：这里提供了默认和手动两种选择，我们选择手动方式。
- TypeScript：这是javascript的一个超集，为javascript添加更多特性。javascript暂时能满足我们需求，我们可以不使用typescript。
- Progressive Web App (PWA) Support：移动端网页应用的支持，如果我们不考虑移动端可不选择。
- Router：实现页面跳转的官方路由，必选。
- Vuex：集中式存储管理应用的所有组件的状态，它是一个状态管理模式。
- Linter / Formatter：使用ESLint来规范代码。
- Unit Testing：单元测试，测试内容主要是组件内每一个函数的返回结果是不是和期望值一样。
- E2E Testing：端到端测试，从用户角度上看，程序相当黑盒子，测试一个应用从头到尾的流程是否和设计时候所想的一样

接下来会要我们选择history模式还是hash模式：
![在这里插入图片描述](https://gitee.com/watchcat2k/pictures_base/raw/master/2020/1/4.PNG)
- hash模式：地址栏中会出现`#`号，在将url作为参数传递时不能满足需求
- history模式：地址栏中不会出现`#`号，在将url作为参数传递时可以满足需求，但需要后台配合，处理404问题。

现在一般都选择history模式。

接下来还要我们选择ESLint标准，我选择了Prettier（译为更美丽的），之后在vscode中，可以直接鼠标右键>源代码操作>“Fix all ESLint auto-fixable problems”使代码规范，如下图：

![在这里插入图片描述](https://gitee.com/watchcat2k/pictures_base/raw/master/2020/1/8.png)

最终我的选择如下所示：

![在这里插入图片描述](https://gitee.com/watchcat2k/pictures_base/raw/master/2020/1/5.PNG)

项目安装完成后，如下图：

![在这里插入图片描述](https://gitee.com/watchcat2k/pictures_base/raw/master/2020/1/6.png)

按照提示运行vue项目，并在浏览器中访问`http://localhost:8080`，则得到下图中的应用：

![在这里插入图片描述](https://gitee.com/watchcat2k/pictures_base/raw/master/2020/1/7.png)

[^1]: 参考自[https://blog.csdn.net/y_0232/article/details/102555209](https://blog.csdn.net/y_0232/article/details/102555209)
