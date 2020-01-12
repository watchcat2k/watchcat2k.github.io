---
layout: post
title: 后台前端解决方案vue-element-admin的安装及使用
date: 2020-01-12 00:00:00
categories: 
- Element-UI-前端UI框架
tags: 
- Vue
- Vue-cli
- Element-UI
description: vue-element-admin 是一个后台前端解决方案，它基于 vue 和 element-ui 实现。它使用了最新的前端技术栈，内置了 i18 国际化解决方案，动态路由，权限验证，提炼了典型的业务模型，提供了丰富的功能组件，它可以帮助你快速搭建企业级中后台产品原型
---



# 简介
vue-element-admin 是一个后台前端解决方案，它基于 vue 和 element-ui 实现。它使用了最新的前端技术栈，内置了 i18 国际化解决方案，动态路由，权限验证，提炼了典型的业务模型，提供了丰富的功能组件，它可以帮助你快速搭建企业级中后台产品原型。更多详情参考[官方文档](https://panjiachen.github.io/vue-element-admin-site/zh/)。

# 安装
```
# 克隆项目
git clone https://github.com/PanJiaChen/vue-element-admin.git

# 进入项目目录
cd vue-element-admin

# 建议不要用 cnpm 安装 会有各种诡异的bug 可以通过如下操作解决 npm 下载速度慢的问题
npm install --registry=https://registry.npm.taobao.org

# 本地开发 启动项目
npm run dev
```

最终效果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200111155559996.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MjcyMjgy,size_16,color_FFFFFF,t_70)

# 基础模板
vue-element-admin项目集成了很多可能用不到的功能，会造成不少的代码冗余。所以我们以vue-admin-template为基础模板，通过复制vue-element-admin项目的内容添加到基础模板中，从而实现符合自己需要的后台管理项目。

```
# 克隆项目
git clone https://github.com/PanJiaChen/vue-admin-template.git

# 进入项目目录
cd vue-admin-template

# 建议不要直接使用 cnpm 安装以来，会有各种诡异的 bug。可以通过如下操作解决 npm 下载速度慢的问题
npm install --registry=https://registry.npm.taobao.org

# 启动服务
npm run dev
```

最终效果：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200111161042347.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MjcyMjgy,size_16,color_FFFFFF,t_70)

# 整合到其它vue项目
我用vue-cli创建了一个vue项目，现在要把该后台管理系统整合vue项目中，我选择的是用复制的方法复制一个个视图文件。

首先其它vue项目要先安装好element-ui，然后把后台管理系统的视图文件根据自己的需要复制vue项目中，在后台管理系统源码的`src`文件夹下，还有该系统自带的一些图标、脚本文件，如下图：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200112204720680.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MjcyMjgy,size_16,color_FFFFFF,t_70)

部分视图文件需要依赖`src/`文件夹里的`icons/`、`utils/`等文件夹里的内容，复制时根据需要复制这些文件夹及内容到其它vue项目中。
