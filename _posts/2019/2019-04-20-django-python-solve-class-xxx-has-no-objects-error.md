---
layout: post
title: 解决在vscode中用python操作数据库模型时出现的Class "xxx" has no 'objects' member错误提示
date: 2019-04-20 00:00:00
categories: 
- Django-web框架
tags: 
- Python
- Django
description: 解决在vscode中用python操作数据库模型时出现的Class "xxx" has no 'objects' member错误提示。
---


# 问题原因  {#reason}
在vscode中用python操作数据库模型时，比如以下代码：
```
article = models.Article.objects.get(pk=1)
```
vscode会提示出现Class "xxx" has no 'objects' member错误。

其实这代码并没有错，直接运行该python程序是可以成功操作数据库模型的。造成错误提示的原因是：vsscode中的python插件默认使用pylint的一个工具，专门用来检测python代码的书写是否有错误和是否符合良好的习惯，而django.db.models.Model的模型层对象在编译时没有objects属性，但是运行时却有，造成我们在编写代码时pylint会报"has no objects attributes"之类的错误。

# 解决办法  {#solution}
把pylint工具换成pylint-django工具。

首先安装pylint-django：
```
pip install pylint-django
```
然后在vscode中，在工具栏中选中“文件 => 首选项 => 设置”，在搜索框中搜索“python.linting.pylintArgs”，如下图：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-04/2019-04-20-1.png)

然后选择在“settings.json”中编辑，然后输入以下代码：
```
"python.linting.pylintArgs" :[       
     "--load-plugins=pylint_django"    
 ]
```
结果如下图：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-04/2019-04-20-2.png)

这样便解决了这个问题。
