---
layout: post
title: 数据挖掘实训周报（一）：windows和ubuntu16.04系统下python3.x和pip3的安装配置
date: 2019-04-11 00:00:00
categories: 
- DM-数据挖掘
tags: 
- 数据挖掘
- Ubuntu
- Python
- Pip
description: windows和ubuntu16.04系统下python3.x和pip3的安装配置。
---



# 前言  {#qianyan}
本次软件工程高级实训的课题是数据挖掘，选择的语言是python，python关于数据挖掘几个基础的包大概是：numpy, scipy, pandas, scikit-learn, statsmodels, matplotlib, xgboost, jupyter。所以，我们要在自己的电脑中安装配置好这些依赖项。

# ubuntu下环境配置  {#ubuntu-setting}
## python3.7的安装  {#install-python37}
我在自己的电脑中利用虚拟机安装了ubuntu 16.04系统，而在ubuntu中python是自带的，自带的版本有两个，分别是python2.7.12和python3.5.2，通过命令
```
python -V		//默认版本
python2 -V	//python2版本
python3 -V	//python3版本
```
可以查看当前ubuntu系统预装的python版本，通常默认使用的python版本是2.7.12，而我决定使用新的python3.7.3版本。

ubuntu安装python3.7.3版本步骤如下：

**步骤一**：首先新建一个空文件夹，在文件夹里打开终端，输入以下命令
```
wget https://www.python.org/ftp/python/3.7.3/Python-3.7.3.tgz
```
那么当前文件夹里就下载得到了Python-3.7.3.tgz压缩包。

 **步骤二**：对该压缩包进行解压，执行以下命令：
```
tar -zxvf Python-3.7.3.tgz
```
**步骤三**：在终端中进入压缩得到的文件夹：
```
cd Python-3.7.3
```
**步骤四**：执行以下命令配置：
```
./configure
```
**步骤五**：执行以下命令进行编译：
```
make
```
**步骤六**：执行以下命令进行安装：
```
sudo make install
```
**步骤七**：查看python是否安装成功：
```
python3.7 -V
```
最终结果如下图：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-04/2019-04-11-3.png)

## 修改默认python版本  {#change-default}
安装成功之后，我们要修改ubuntu系统默认的python版本，使得默认的版本指向python3.7.3，所以执行以下命令

```
echo alias python=python3.7 >> ~/.bashrc
```
修改配置文件，然后再执行以下命令即可
```
source ~/.bashrc
```
此时执行以下命令查看是否成功：
```
python -V
```
结果如下：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-04/2019-04-11-4.png)

## 依赖包的安装  {#install-dependence}
然后我们要安装pip，利用pip可以方便迅速地安装各种所需要的package。通过以下命令安装pip3
```
sudo apt install python3-pip
```
安装完成之后，执行以下命令，查看pip3是否成功安装
```
pip3 -V
```
默认安装的pip3版本较低，我们还要执行以下命令升级pip3：
```
sudo pip3 install --upgrade pip
```

pip3升级后，我们查看pip3是否成功升级：
```
pip3 -V
```
发现终端报错，如下图：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-04/2019-04-20-3.png)

我们用vim做以下修改（按'i'进入插入模式编辑，按"Esc"推出插入模式，然后按":wq"保存退出vim）：
```
sudo vim /usr/bin/pip3
```
然后把里面的代码改成以下：
```
from pip import __main__
if __name__ == '__main__':
    sys.exit(__main__._main())
```
如下图所示：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-04/2019-04-20-4.png)

之后再检查pip3版本就没问题了。

之后便是利用pip3进行各种packag

之后便是利用pip3进行各种package的安装，只需一条命令，就可以把前言提到的各种包进行安装。
```
sudo pip3 install numpy scipy pandas scikit-learn statsmodels matplotlib xgboost jupyter
```
安装成功后，结果如下：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-04/2019-04-11-5.png)

# Window下环境配置  {#window-setting}
前往[python官网](https://www.python.org/downloads/)下载适合window系统的3.7版本的python，然后打开安装程序进行安装，如下图：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-04/2019-04-11-1.png)

注意，安装时勾选“自动添加Path到系统变量”，如上图红线所示。

安装完成后，pip也会自动安装，而且是最新版本。而其它依赖包的安装与ubuntu下的安装方法类似，执行以下命令：
```
pip3 install numpy scipy pandas scikit-learn statsmodels matplotlib jupyter
```
注意，**xgboost**依赖项在window里是不能用上述命令安装的，它的安装方法如下：
- 前往[https://www.lfd.uci.edu/~gohlke/pythonlibs/#xgboost](https://www.lfd.uci.edu/~gohlke/pythonlibs/#xgboost)，因为我的python版本为3.7，所以下载文件xgboost‑0.82‑cp37‑cp37m‑win32.whl
- 在终端中进入xgboost‑0.82‑cp37‑cp37m‑win32.whl所在的文件夹，然后执行以下命令即可
```
pip install xgboost‑0.82‑cp37‑cp37m‑win32.whl
```

# numpy
NumPy是Python语言的一个扩展程序库，支持大量的维度数组与矩阵运算，此外也针对数组运算提供大量的数学函数库。numpy教程参考如下：[http://www.runoob.com/numpy/numpy-tutorial.html](http://www.runoob.com/numpy/numpy-tutorial.html)。

numpy在程序中引入方法为：
```
import numpy as np 
```
接着就可以在程序中使用变量“np”进行各种操作：

- 创建数组：
```
a = np.array([1,2,3])  
```
- 创建2*2数组
```
a = np.array([[1,  2],  [3,  4]])  
```
- 创建长度为10的元素全为0或1的数组
```
a = np.zeros(10)
a = np.ones(10)
```
- 创建5*5的随机数组，注意两维的时候，要两个random，两个括号
```
a = np.random.random((5, 5))
```
- 矩阵乘法
```
array1.dot(array2)
```

# scipy
SciPy是构建在numpy的基础之上的，它提供了许多的操作numpy的数组的函数。SciPy是一款方便、易于使用、专为科学和工程设计的python工具包，它包括了统计、优化、整合以及线性代数模块、傅里叶变换、信号和图像图例，常微分方差的求解等。

关于scipy的学习可以参考官方文档[https://docs.scipy.org/doc/scipy/reference/index.html](https://docs.scipy.org/doc/scipy/reference/index.html)
关于scipy的函数用法总结可以参考博客[https://blog.csdn.net/q583501947/article/details/76735870](https://blog.csdn.net/q583501947/article/details/76735870)

# pandas
pandas是基于NumPy的一种工具，该工具是为了解决数据分析任务而创建的，它被广泛用于快速分析数据，以及数据清洗和准备等工作。Pandas 纳入了大量库和一些标准的数据模型，提供了高效地操作大型数据集所需的工具。

关于pandas的学习可以参考这篇博客[https://blog.csdn.net/qq_42156420/article/details/82813482](https://blog.csdn.net/qq_42156420/article/details/82813482)

# scikit-learn
scikit-learn，简记sklearn，是用python实现的机器学习算法库。sklearn可以实现数据预处理、分类、回归、降维、模型选择等常用的机器学习算法。sklearn是基于NumPy, SciPy, matplotlib的。

sklearn的学习可以参考官方文档[https://scikit-learn.org/stable/#](https://scikit-learn.org/stable/#)

# statsmodels
 statsmodels是一个Python库，用于拟合多种统计模型，执行统计测试以及数据探索和可视化。statsmodels包含更多的“经典”频率学派统计方法。
 
关于statsmodels的学习可以参考这篇博客[https://www.jianshu.com/p/e45558ccf533](https://www.jianshu.com/p/e45558ccf533)

# matplotlib
Matplotlib是一个Python的2D绘图库，它以各种硬拷贝格式和跨平台的交互式环境生成出版质量级别的图形。它可与 NumPy 一起使用，提供了一种有效的 MatLab 开源替代方案。

matplotlib的学习可以参考这篇博客[http://www.runoob.com/numpy/numpy-matplotlib.html](http://www.runoob.com/numpy/numpy-matplotlib.html)

# xgboost
XGBoost是提升方法中的一个可扩展的机器学习系统。XGBoost在许多机器学习和数据挖掘问题中产生了广泛的影响。2015年发表在Kaggle竞赛的博客的29个冠军解决方案中，有17个是使用XGBoost解决的，其中有8个是仅使用了XGBoost方法去训练模型，剩余的是用XGBoost和其他模型相结合使用的。

xgboost的原理介绍如下[https://blog.csdn.net/qq_24519677/article/details/81809157](https://blog.csdn.net/qq_24519677/article/details/81809157)
xgboost的使用方法如下[https://blog.csdn.net/qq_24519677/article/details/81869196](https://blog.csdn.net/qq_24519677/article/details/81869196)

# jupyter
Jupyter Notebook是一个交互式笔记本，支持运行40多种编程语言。Jupyter Notebook的本质是一个Web应用程序，便于创建和共享文学化程序文档，支持实时代码，数学方程，可视化和markdown。用途包括：数据清理和转换，数值模拟，统计建模，机器学习等等。

jupyter的使用可以参考这篇博客[https://blog.csdn.net/gubenpeiyuan/article/details/79252402](https://blog.csdn.net/gubenpeiyuan/article/details/79252402)

在终端中执行以下命令，就可打开jupyter网页编辑器
```
jupyter notebook  
```
在下图中，点击“upload”便可上传本地的文件，然后便可打开并编辑该文件，如下图：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-04/2019-04-11-6.png)

还可以点击“new”创建新的文件。
