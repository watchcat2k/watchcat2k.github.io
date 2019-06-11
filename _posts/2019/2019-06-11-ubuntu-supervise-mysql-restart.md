---
layout: post
title: Ubuntu 下定时监测 MySQL 进程终止后自动重启的方法
date: 2019-06-11 00:00:00
categories: 
- Server-服务器相关
tags: 
- Server
- Mysql
description: 最近在服务器上做一个小项目，发现 mysql 服务总是莫名奇妙地自动关闭，每次都要重新手动开启 mysql 服务，现在这里有更好的解决办法。
---



最近在服务器上做一个小项目，发现 mysql 服务总是莫名奇妙地自动关闭，每次都要重新手动开启 mysql 服务，现在这里有更好的解决办法。

# mysql 进程终止原因
根据这篇博客 [https://blog.csdn.net/qq_40312076/article/details/80060966](https://blog.csdn.net/qq_40312076/article/details/80060966)，在 ubuntu 中 mysql 服务莫名奇妙地被关闭可能与 ubuntu 系统自动更新有关，所以我们可以尝试手动关闭 ubuntu 系统的自动更新：Settings > Software & updates，设置成如下图形式：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-06/2019-06-11-1.png)

设置完成之后，mysql 服务确实保持了很长一段时间的开启状态，没有被关闭。

不过造成 mysql 服务终止的原因有很多，不仅仅与 ubuntu 系统自动更新有关，我们应该采用更广泛的、有效的解决办法，即 mysql 服务监听器。

# mysql 服务监听
以下内容转载自 [https://cloud.tencent.com/developer/article/1004643](https://cloud.tencent.com/developer/article/1004643)。

## 编写脚本
shell脚本的后缀为sh，在任何位置新建一个脚本文件，我选择在 /etc/mysql 目录下新建一个 `listen.sh` 文件。

执行如下命令：

```
cd /etc/mysql
touch listen.sh
vi listen.sh
```

进入到vi中，我们添加如下脚本内容：

```
#!/bin/bash
pgrep mysqld &> /dev/null
if [ $? -gt 0 ]
then
echo "`date` mysql is stop"
service mysql start
else
echo "`date` mysql running"
fi
```

其中 pgrep mysqld 是监测mysqld服务的运行状态，&> /dev/null 是将其结果输出到空文件，也就是不保存输出信息

$? 是拿到上一条命令的运行结果，-gt 0 是判断是否大于0，后面则是输出时间到日志文件，然后启动mysql，否则不启动mysql

编辑完了.sh文件之后，我们首先要对其进行授权，增加可执行的权限：
```
sudo chmod 777 listen.sh
```

保存好了，那么我们执行如下的命令，来测试一下。
```
root@iZ28uogb3laZ:/etc/mysql# vi listen.sh
root@iZ28uogb3laZ:/etc/mysql# pgrep mysqld
3359
root@iZ28uogb3laZ:/etc/mysql# chmod 777 listen.sh
root@iZ28uogb3laZ:/etc/mysql# ./listen.sh
Sun Aug 16 16:44:58 CST 2015 mysql running
root@iZ28uogb3laZ:/etc/mysql# sudo service mysql stop
mysql stop/waiting
root@iZ28uogb3laZ:/etc/mysql# ./listen.sh
Sun Aug 16 16:45:17 CST 2015 mysql is stop
mysql start/running, process 4084
root@iZ28uogb3laZ:/etc/mysql# ./listen.sh
Sun Aug 16 16:45:24 CST 2015 mysql running
root@iZ28uogb3laZ:/etc/mysql#
```
运行脚本测试一下，显示mysql正在运行。把mysql关掉，运行脚本，便会检测到mysql已关闭，然后重新启动了mysql，再次运行，便会发现mysql正常运行了。

## 修改日志输出
接下来我们把输出的内容保存到日志里。修改脚本文件如下

```
#!/bin/bash
pgrep mysqld &> /dev/null
if [ $? -gt 0 ]
then
echo "`date` mysql is stop" >> /var/log/mysql_listen.log
service mysql start
else
echo "`date` mysql running" >> /var/log/mysql_listen.log
fi
```
这样，每执行一次脚本，输出结果都会被保存到 /var/log/mysql_listen.log 中了。

## 添加定时任务
脚本可以顺利执行了，那么我们就需要定时调用一下这个脚本来运行了，我们需要用到 cron。

首先我们需要编辑一下corn调度表格，命令如下：
```
crontab -e
```
如果你是第一次编辑这个，他会让你选择文件打开方式，随便选一个数字就好了。

比如我们用GNU打开的，我们就在它的最后一行添加下面的一句话即可。

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-06/2019-06-11-2.png)

文字版本：
```
*/5 * * * * /etc/mysql/mysql_listen.sh
```

/5代表五分钟执行一次，后面的四个点依次代表了，小时，日，月，星期。如果想要时间长一些，比如一小时调度一次，那就设置一下后面第一个*就好了。

好，保存一下，重启cron服务。
```
service cron restart
```
嗯，调度任务已经添加进去了，这样，每五分钟系统就会调用一下刚才写的那个脚本。

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-06/2019-06-11-3.png)

内容：
```
Sun Aug 16 15:39:12 CST 2015 mysql running
Sun Aug 16 15:40:01 CST 2015 mysql running
Sun Aug 16 15:45:02 CST 2015 mysql running
Sun Aug 16 15:50:01 CST 2015 mysql running
Sun Aug 16 15:55:01 CST 2015 mysql running
Sun Aug 16 16:00:01 CST 2015 mysql running
Sun Aug 16 16:05:01 CST 2015 mysql running
Sun Aug 16 16:10:01 CST 2015 mysql running
Sun Aug 16 16:15:01 CST 2015 mysql running
Sun Aug 16 16:20:01 CST 2015 mysql running
Sun Aug 16 16:25:01 CST 2015 mysql running
Sun Aug 16 16:30:01 CST 2015 mysql running
Sun Aug 16 16:35:01 CST 2015 mysql running
Sun Aug 16 16:40:01 CST 2015 mysql running
Sun Aug 16 16:51:04 CST 2015 mysql running
```


