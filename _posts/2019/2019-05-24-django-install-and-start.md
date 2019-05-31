---
layout: post
title: Django框架学习（一）：Django的安装和初步的使用
date: 2019-05-24 00:00:00
categories: 
- Django-web框架
tags: 
- Python
- Django
description: Django 是最受欢迎的 Python Web 框架之一，这里介绍 Django 的安装和初步的使用。
---


# 安装
安装Django之前，确保系统中已经安装了python3.x和最新版的pip3，python3 和 pip3 的配置方法参考[https://blog.csdn.net/qq_36272282/article/details/89217005](https://blog.csdn.net/qq_36272282/article/details/89217005)。

window下，直接执行以下命令：
```
pip3 install django
```
ubuntu16.04下，执行以下命令：
```
sudo pip3 install django
```
使用以下命令查看Django是否安装成功：
```
django-admin --version
```

# 新建一个项目
使用以下命令创建名为HelloWorld的项目：
```
django-admin startproject HelloWorld
```
这样的话可以自动生成HelloWorld文件夹，里面则是Django的项目文件，文件结构如下：
```
|-- HelloWorld
    |-- manage.py
    |-- HelloWorld
        |-- settings.py
        |-- urls.py
        |-- wsgi.py
        |-- __init__.py
```
目录说明：
- HelloWorld: 项目的容器。
- manage: 一个实用的命令行工具，可让你以各种方式与该 Django 项目进行交互。 
- HelloWorld/__init__.py: 一个空文件，告诉 Python 该目录是一个 Python 包。
- HelloWorld/settings.py: 该 Django 项目的设置/配置。
- HelloWorld/urls.py: 该 Django 项目的 URL 声明; 一份由 Django 驱动的网站"目录"。
- HelloWorld/wsgi.py: 一个 WSGI 兼容的 Web 服务器的入口，以便运行你的项目。

进入HelloWorld文件夹，执行以下命令即可启动服务器：
```
python3 manage.py runserver
```
若django项目是部署在服务器，那么运行命令加上端口号如下：
```
python3 manage.py runserver 0.0.0.0:8000
```
通过`127.0.0.1:8000`可在服务器本地访问该项目，通过`服务器地址:8000`可在外部访问

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-05/2019-05-24-1.png)

# 新建一个应用
 使用以下命令创建一个名为 blog 的应用：
```
django-admin startapp blog
```
往后主要的代码编写都是在应用里面进行。
创建应用后会生成一个 blog 文件夹，文件结构如下：
```
|-- blog
    |-- admin.py
    |-- apps.py
    |-- models.py
    |-- tests.py
    |-- views.py
    |-- __init__.py
    |-- migrations
        |-- __init__.py
```
- admin 是基于 web 的管理工具，可以管理本应用的数据库数据
- models 是模型文件，在里面编写数据库模型类
- test 是单元测试文件，可以在里面编写单元测试类和函数
- views 是视图文件，数据库操作、静态文件的返回等都在此处进行

创建应用之后，要在项目的 setting 文件上添加对应的 app 名称，否则新建的 app 无法正常使用。 
```
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'blog'
]
```

# URL配置
之前我们新建了一个 HelloWorld 项目，又新建了一个 blog 应用，现在的文件结构如下：
```
|-- HelloWorld
    |-- db.sqlite3
    |-- manage.py
    |-- blog
    |   |-- admin.py
    |   |-- apps.py
    |   |-- models.py
    |   |-- tests.py
    |   |-- views.py
    |   |-- __init__.py
    |   |-- migrations
    |       |-- __init__.py
    |-- HelloWorld
        |-- settings.py
        |-- urls.py
        |-- wsgi.py
        |-- __init__.py
```
我们还没有为新建的 blog 应用配置 url， 现在我们先在 blog 文件夹下新建一个urls.py文件，内容如下：
```
from django.contrib import admin
from django.urls import path
from . import views

urlpatterns = [
    path('index', views.blog_index),
]
```
并且修改 blog 文件夹下的 views 文件，代码如下：
```
from django.shortcuts import render
from django.http import HttpResponse

# Create your views here.

def blog_index(request):
    return HttpResponse("This is blog index")
```
最后在 HelloWorld 文件夹下的 urls 文件中，增加一条 path，如下：
```
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('blog/', include('blog.urls'))
]
```
这样就配置好了 url，HelloWorld 下的urls.py负责将不同的 url 分配到不同应用，而应用 blog 下的urls.py负责将不同的 url 匹配给不同的视图函数。查看 url 地址 127.0.0.1:8000/blog/index，结果如下

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-05/2019-05-24-2.png)

# 模板
在应用 blog 下新建一个文件夹 templates，里面新建一个 index.html 文件，内容如下：
```
<h1>{{ hello }}</h1>
```
应用 blog 的文件结构如下：
```
|-- blog
    |-- admin.py
    |-- apps.py
    |-- directoryList.md
    |-- models.py
    |-- tests.py
    |-- urls.py
    |-- views.py
    |-- __init__.py
    |-- migrations
    |   |-- __init__.py
    |-- templates
    |   |-- index.html
```
然后修改应用 blog 下的 views 视图文件，如下：
```
from django.shortcuts import render
from django.http import HttpResponse

# Create your views here.

def blog_index(request):
    context          = {}
    context['hello'] = 'Hello World!'
    return render(request, 'index.html', context)
```
结果如下：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-05/2019-05-24-3.png)
