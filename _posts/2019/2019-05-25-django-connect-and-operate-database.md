---
layout: post
title: Django框架学习（二）：连接MySQL数据库并对数据进行操作
date: 2019-05-25 00:00:00
categories: 
- Django-web框架
tags: 
- Python
- Django
- Mysql
description: Django 是最受欢迎的 Python Web 框架之一，这里介绍 Django 连接 MySQL 数据库并对数据进行操作。
---



Django规定，如果要使用数据库模型，必须要创建一个app。
# MySQL的安装配置
## window系统 
前往MySQL官网[https://dev.mysql.com/downloads/mysql/](https://dev.mysql.com/downloads/mysql/)下载window版的MySQL Community Server 8.0.15的压缩包，如下图：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-04/2019-04-21-1.png)

把压缩包解压到某一路径，我解压到`D:\Program Files`，在刚刚解压的文件夹`D:\Program Files\mysql-8.0.15-winx64`内，新建一个data文件夹，并新建一个my.ini文件，里面的内容如下：
```
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8
 
[mysqld]
# 设置3306端口
port = 3306
# 设置mysql的安装目录
basedir=D:\Program Files\mysql-8.0.15-winx64
# 设置 mysql数据库的数据的存放目录，即刚刚新建的data文件夹内
datadir=D:\Program Files\mysql-8.0.15-winx64\data
# 允许最大连接数
max_connections=20
# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
```

以管理员身份运行终端，进入mysql目录下的bin文件夹：
```
cd D:\Program Files\mysql-8.0.15-winx64\bin
```
运行一下命令初始化数据库：
```
mysqld --initialize --console
```
执行完成后会产生root用户默认密码，这个密码务必要记住，后面会用到：
```
...
2018-04-20T02:35:05.464644Z 5 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: APWCY5ws&hjQ
...
```
然后执行安装命令：
```
mysqld install
```
然后便可以开启mysql服务：
```
net start mysql
```
若开启服务时出现系统错误2，则先卸载再重新安装：
```
mysqld remove
mysqld install
```
开启服务后，执行以下命令进入mysql的终端，密码为之前初始化数据库时生成的默认密码：
```
mysql -uroot -p
```
然后执行以下命令修改密码永不过期，这样可以避免密码失效的问题：
```
ALTER USER 'root'@'localhost' IDENTIFIED BY 'password' PASSWORD EXPIRE NEVER; 
```
然后执行以下命令修改密码模式，否则后面再Django中连接数据库时会出现`RuntimeError: cryptography is required for sha256_password or caching_sha2_password`错误：
```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```
然后刷新权限：
```
FLUSH PRIVILEGES;
```
之前的默认密码不好记，这里我们改为自己熟悉的密码，这里我直接改为"123456"：
```
alter user 'root'@'localhost' identified by '123456';
```
最后，我们要在mysql终端中创建一个database：
```
create database my_blog_db;
```
查看和删除database的命令如下：
```
show databases;
drop database my_blog_db;
```
这样，在后面的Django中，我们就可以连接名为`my_blog_db`的database了，

## ubuntu系统
前往mysql官网下载[https://dev.mysql.com/downloads/repo/apt/](https://dev.mysql.com/downloads/repo/apt/)deb包，如下图：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-04/2019-04-21-2.png)

下载完成后，在deb包所在的文件夹下打开终端，执行以下命令：
```
sudo dpkg -i mysql-apt-config_0.8.12-1_all.deb
```
然后直接选择”OK“，如下图：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-04/2019-04-21-3.png)

然后执行更新语句：
```
sudo apt update
```
然后进行安装:
```
sudo apt install mysql-server
```
接着便是在安装界面中输入root密码，这个密码务必要记住，后面会用到。

然后是阅读配置MySQ社区服务器条例，按键盘上的”右“方向键，即可选择”OK“按钮，回车即可。

接下来是重要的密码加密方式，我们选第二个，如下图：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-04/2019-04-21-4.png)

因为新版mysql的密码加密方式很多地方都暂时不支持，所以选第二个旧版的密码加密方式。

安装完成之后，便可以在终端中登录进mysql，密码是123456：
```
mysql –u root –p
```
然后创建一个database
```
create database my_blog_db
```
这样，在后面的Django中，我们就可以连接名为`my_blog_db`的database了，

查看和删除database的命令如下：
```
show databases;
drop database my_blog_db;
```
最后是mysql服务的开启和关闭：
```
service mysql start
service mysql stop
```
如果提示系统权限限制，就先获取 root 权限，再开启 mysql 服务：
```
su root
```

# Django连接MySQL
## window系统
首先安装PyMySQL：
```
pip3 install PyMySQL
```
然后安装mysqlclient：

前往[https://www.lfd.uci.edu/~gohlke/pythonlibs/#mysqlclient](https://www.lfd.uci.edu/~gohlke/pythonlibs/#mysqlclient)，根据系统的python版本选择对应的mysqlclient文件，如下图：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-04/2019-04-20-5.png)

下载到本地后，在终端中进入该文件所在的文件夹，执行以下命令即可：
```
pip3 install mysqlclient-1.4.2-cp37-cp37m-win32.whl
```
接着便是修改settings.py文件，如下：
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',    # 数据库引擎
        'NAME': 'my_blog_db',    # 之前在mysql终端中创建的database
        'USER': 'root',         # 数据库用户名
        'PASSWORD': '123456',     # 数据库密码
        'HOST': 'localhost',    # 数据库运行的主机
        'PORT': '3306',         # 数据库服务开启的端口
    }
}
```
写了models.py文件后（之后介绍如何写models.py操作数据库），执行以下命令：
```
python manage.py makemigrations
```
若出现`django.db.utils.OperationalError: (2059, < NULL >)`错误，则在django项目的__init__.py文件中添加：
```
import pymysql
pymysql.install_as_MySQLdb()
```
若出现`django.core.exceptions.ImproperlyConfigured: mysqlclient 1.3.3 or newer is required; you have 0.7.11`错误，则找到Python安装路径下的`Python37-32\Lib\site-packages\django\db\backends\mysql\base.py`文件，将文件中的以下代码注释：
```
if version < (1, 3, 3):
    raise ImproperlyConfigured("mysqlclient 1.3.3 or newer is required; you have %s" % Database.__version__)
```
若出现`AttributeError: 'str' object has no attribute 'decode'`错误，则找到python安装路径下的`Python37-32\Lib\site-packages\django\db\backends\mysql\operations.py`，在第146行改动以下代码，即把decode改为encode：
```
（line146）：query = query.encode(errors='replace')
```
最后执行：
```
python manage.py migrate
```
成功之后，便可以在view.py中进行数据库的增删改查各种操作


## ubuntu系统
ubuntu系统自带了python2和python3，所以写命令时，务必把python命令写成python3，pip命令也写成pip3。

首先安装PyMySQL：
```
pip3 install PyMySQL
```
然安装libmysqld-dev：
```
sudo apt-get install libmysqld-dev
```
有可能会出现以下错误：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-04/2019-04-21-5.png)

这是我们要先安装对应版本的libmysqlclient-dev：
```
sudo apt-get install libmysqlclient-dev=5.7.25-0ubuntu0.16.04.2
```
然后再重新安装libmysqld-dev。

最后安装mysqlclient：
```
sudo pip3 install mysqlclient
```
接着便是修改settings.py文件，如下：
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',    # 数据库引擎
        'NAME': 'my_blog_db',    # 之前在mysql终端中创建的database
        'USER': 'root',         # 数据库用户名
        'PASSWORD': '123456',     # 数据库密码
        'HOST': 'localhost',    # 数据库运行的主机
        'PORT': '3306',         # 数据库服务开启的端口
    }
}
```
写了models.py文件后（之后介绍如何写models.py操作数据库），执行以下命令：
```
python3 manage.py makemigrations
```
若出现错误，参考前面window系统下的错误处理方法。

最后执行：
```
python3 manage.py migrate
```
成功之后，便可以在view.py中进行数据库的增删改查各种操作。

# 数据库的使用
## 定义模型
Django 规定，如果要使用模型，必须要创建一个app。我们使用以下命令创建一个 TestModel 的 app:
```
django-admin startapp TestModel
```
并且记得把 app 名称添加到 setting  文件里面。

然后我们修改 TestModel/models.py 文件，代码如下：
```
# models.py
from django.db import models
 
#用户
class User(models.Model):    
    user_id = models.AutoField(primary_key=True)    
    user_phone = models.CharField(max_length=11)   
    user_password = models.CharField(max_length=20)    
    user_icon = models.BinaryField(default=None, null=True)    
    user_balance = models.IntegerField(default=100)   
    user_fillln = models.SmallIntegerField(default=0)

# 学生
class Student(models.Model):
    ......
```
模型字段的定义可以增加一些修饰符，如：
- 声明主键 primary_key=True
- 默认值为空 default=None, null=True
- 字符串长度限制 max_length=11
- 自动添加当前时间 auto_now_add=True
- ......

然后再命令行中执行：
```
python manage.py makemigrations  # 让 Django 知道我们在我们的模型有一些变更
python manage.py migrate   # 创建表结构
```

## 操作数据库
我们通过匹配 url，分配给 view 中的函数，使用 view 中的函数进行数据库操作和静态文件返回等等。

### 添加数据
添加数据需要先创建对象，然后再执行 save 函数，相当于SQL中的INSERT：
```
new_user = models.User(
    user_phone='15432458777',
    user_password='123456'
)
new_user.save()
```
默认字段可以指明，也可以省略。

### 获取数据
Django提供了多种方式来获取数据库的内容，如下代码所示：
```
# 获取多个对象用filter，并可按 id 从小到大排序
filter_users = models.User.objects.filter(user_phone='15487587777').order_by("id")

# 获取单个对象用get
get_user = models.User.objects.get(user_id=1)

# 获取全部对象用all
all = models.User.objects.all()

# 注意，获取到的对象后，还有提取出里面的值
user_id = get_student.user_id
student_number = get_student.student_number
student_name = get_student.student_name

# 如果获取到的是多个对象，要利用循环获取值
list = []
for filter_user in filter_users:
    list.append({
        'user_id': filter_user.user_id,
        ...
    })
```

### 更新数据
```
# 修改其中一个id=1的name字段，再save，相当于SQL中的UPDATE
user1 = model.User.objects.get(id=1)
user1.name = 'Google'
user1.save()

# 另外一种方式
model.User.objects.filter(id=1).update(name='Google')

# 修改所有的列
model.User.objects.all().update(name='Google')
```

### 删除数据
```
# 删除id=1的数据
user1= model.User.objects.get(id=1)
user1.delete()

# 另外一种方式
model.User.objects.filter(id=1).delete()

# 删除所有数据
model.User.objects.all().delete()
```

