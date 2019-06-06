---
layout: post
title: Django框架学习（三）：Django Admin 管理工具
date: 2019-06-06 00:00:00
categories: 
- Django-web框架
tags: 
- Python
- Django
- Admin
description: Django 提供了基于 web 的数据库管理工具。利用 admin 管理工具，我们可以很方便地在网页上查看和操作数据库，而无需第三方的数据库管理软件。
---



Django 提供了基于 web 的数据库管理工具。利用 admin 管理工具，我们可以很方便地在网页上查看和操作数据库，而无需第三方的数据库管理软件。

# 激活管理工具
通常新建一个 django 项目后，在 `urls.py` 文件会默认激活管理工具，如下：
```
# urls.py
from django.conf.urls import url
from django.contrib import admin
 
urlpatterns = [
    url(r'^admin/', admin.site.urls),
]
```

# 创建超级用户
通过以下命令创建超级用户：
```
python manage.py createsuperuser
```
然后命令行的提示如下：
```
Username (leave blank to use 'root'): root
Email address: xxx@xxx.com
Password: xxxxxx
Password (again): xxxxxx
Superuser created successfully.
```
我们创建了一个用户名为 `root`，密码为 `xxxxxx` 的超级用户。

# 注册数据模型
我们要修改 django 应用的 `admin.py` 文件，在里面注册我们声明好的数据库模型。 

比如，我在 `models.py` 文件声明了一个学生模型，如下：
```
#学生
class Student(models.Model):
    student_id = models.AutoField(primary_key=True)
    user_id = models.IntegerField()
    student_number = models.CharField(max_length=20)
    student_name = models.CharField(max_length=20)
    student_university = models.CharField(max_length=50)
    student_academy = models.CharField(max_length=50)
    student_gender = models.SmallIntegerField(default=0)
```

然后，我们在 `admin.py` 注册该模型，如下：
```
from django.contrib import admin
from . import models

# Register your models here.

class StudentAdmin(admin.ModelAdmin):
    list_display = ('student_id', 'user_id', 'student_number', 'student_name', 'student_university', 'student_academy', 'student_gender')
    
admin.site.register(models.Student, StudentAdmin)
```

# 使用管理工具
完成上述工作之后，先利用下列命令开启 django 服务：
```
python manage.py runserver
```
然后便可在浏览器上访问 `127.0.0.1:8000/admin/` 使用 admin 管理工具，如下图：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-06/2019-06-06-1.png)

输入之前我们创建超级用户是的用户名和密码即可登录，之后的界面如下：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-06/2019-06-06-2.png)

点击 `student` 即可管理学生表，如下：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-06/2019-06-06-3.png)

可以点击右边的 `add` 按钮添加数据，如下：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-06/2019-06-06-4.png)

还可以更改、删除、查看数据等等。

除此之外，还可以在 `admin.py` 注册多个模型以进行管理。
