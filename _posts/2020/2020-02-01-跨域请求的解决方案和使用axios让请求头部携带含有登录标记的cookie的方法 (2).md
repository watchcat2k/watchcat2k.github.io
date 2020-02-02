---
layout: post
title: 跨域请求的解决方案和使用axios让请求头部携带含有登录标记的cookie的方法
date: 2020-02-01 00:00:00
categories: 
- Server-服务器相关
tags: 
- Server
- Django
- Axios
- Cookie
description: 跨域请求的解决方案和使用axios让请求头部携带含有登录标记的cookie的方法
---



# 跨域与同源
当一个请求url的协议、域名、端口三者之间任意一个与当前页面url不同即为跨域；相反地，所谓同源就是两个页面具有相同的协议，主机和端口号。若一个项目的前后端是分离的，那么前后端部署时就可能部署到不同的域名下或相同域名下的不同端口，那么这时的前后端是跨域的；若前后端合并在一起并部署在同域名下的同端口，那么两者同源。

# 跨域请求的解决方案
例如，我使用django框架搭建了API请求后端，并部署在一台云服务器上，而同时，我在本地电脑上用vue开发了一个前端页面，现在我想在前端页面上发起post请求去请求服务器上的API，由于前后端发生了跨域，这时前端的请求是无法获取后端返回的数据的。

在django中的解决方法是：
安装`django-cors-headers`：
```
pip install django-cors-headers
```
然后在`setting.py`中注册该应用，如下:
```
INSTALLED_APPS = (
...
'corsheaders',   
...
)
```
然后在中间件中注册：
```
MIDDLEWARE_CLASSES
= (
...   
'corsheaders.middleware.CorsMiddleware',
'django.middleware.common.CommonMiddleware',   
...
)
```
注意注册的顺序！！！，不能把该中间件注册太靠后，不然不起作用，我的顺序如下：
![在这里插入图片描述](https://gitee.com/watchcat2k/pictures_base/raw/master/2020/2/1-1.png)

然后再`setting.py`末尾增加以下内容：
```
CORS_ORIGIN_ALLOW_ALL = True
CORS_ALLOW_CREDENTIALS = True
CORS_ORIGIN_WHITELIST = ()
CORS_ALLOW_HEADERS = (
    "*"
)
```
其中要注意`CORS_ALLOW_CREDENTIALS = True`这一项，这一项指是否允许前端的请求头部携带验证的cookie信息，具体用法后面会提及。

完成上述配置，就可以在本地电脑上访问云服务器上的api。

# 跨域下的登录保持
例如，在django后端的登录API中，使用的session来保存用户的登录信息，那么前端成功访问了登录的API后，会在响应头部的`set-cookie `中发现后端设置的`sessionid`，如下图：
![在这里插入图片描述](https://gitee.com/watchcat2k/pictures_base/raw/master/2020/2/1-2.png)

正常来说，当我们登录后，若我们进行登陆后的操作，前端必须将这个sessionid放在请求头部里，以让后端能辨别出发出请求的是刚刚登录的用户，这样整个系统才能正常运行。 但是在跨域的情况下，出于安全性的考虑，由于CORS的限制，为了防止出现跨域攻击，前端一般是无法直接获取`Set-Cookie `里的值，为了实现登录的保持，我们需要一些跨域下的解决方案。

由于js不允许手动设置请求头的cookie，且我们很难获取`Set-Cookie`里的值，那么想要通过获取`sessionid` 并手动设置请求头的方法行不通。

Axios解决方法是：
在vue项目中安装了axios的情况下，在`main.js `中增加这样一条设置：
```
axios.defaults.withCredentials = true;
```
相应地，在django后端框架的`setting.py`中，设置类似的属性：
```
CORS_ALLOW_CREDENTIALS = True
```

这样的话，当我们登录后，再想要进行其它API的请求是，axios会自动帮我们把登陆时相应头里的`Set-Cookie`的值添加到请求头，如下图： 
![在这里插入图片描述](https://gitee.com/watchcat2k/pictures_base/raw/master/2020/2/1-3.png)

这样便能成功将`sessionid`传给后端并保持登录状态。

注意！经过测试，在跨域情况下，上述保持登录的解决方法，适用于前后端部署在相同域名下的不同端口的情况，若前后端部署在不同域名下，还是无法通过上述方法保持登录。
