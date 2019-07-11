---
layout: post
title: 数据挖掘作业（二）：使用随机森林 Random Forest 完成一个回归任务
date: 2019-07-11 00:00:00
categories: 
- DM-数据挖掘
tags: 
- 数据挖掘
- Python
- 随机森林
- 决策树
description: 这是一个回归任务：给定10万个数据样本作为训练集合，每个样本都有13个特征，要预测包含10915121个数据样本的测试集的标签(范围不限)。该项目要求我们设计并实现一个并行决策树算法，即随机森林Random Forest算法。
---



# 题目
本次数据挖掘比赛来源于 kaggle，地址为 [https://www.kaggle.com/c/dm2019springproj2/overview](https://www.kaggle.com/c/dm2019springproj2/overview)。

这是一个回归任务：给定10万个数据样本作为训练集合，每个样本都有13个特征，要预测包含10915121个数据样本的测试集的标签(范围不限)。该项目要求我们设计并实现一个并行决策树算法，即随机森林Random Forest算法。

# 决策树
为了了解随机森林是怎么实现的，我们必须先弄明白决策树是怎么实现的。

## 决策树概念

决策树是一个树结构（可以是二叉树或非二叉树）。其每个非叶节点表示一个特征属性上的测试，每个分支代表这个特征属性在某个值域上的输出，而每个叶节点存放一个类别。使用决策树进行决策的过程就是从根节点开始，测试待分类项中相应的特征属性，并按照其值选择输出分支，直到到达叶子节点，将叶子节点存放的类别作为决策结果。如下图所示：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-07/2019-07-11-1.png)

## 决策树构造
构造决策树的关键步骤是分裂属性。所谓分裂属性就是在某个节点处按照某一特征属性的不同划分构造不同的分支，其目标是让各个分裂子集尽可能地“纯”。尽可能“纯”就是尽量让一个分裂子集中待分类项属于同一类别。分裂属性分为三种不同的情况：
1. 属性是离散值且不要求生成二叉决策树。此时用属性的每一个划分作为一个分支。
2. 属性是离散值且要求生成二叉决策树。此时使用属性划分的一个子集进行测试，按照“属于此子集”和“不属于此子集”分成两个分支。
3. 属性是连续值。此时确定一个值作为分裂点split_point，按照>split_point和<=split_point生成两个分支。

而下图的分裂方式就不够“纯”，它的左右叶子节点有重复的分裂属性 `<=30` 和 `>30`：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-07/2019-07-11-2.png)

所以构造决策树的关键是选择最优划分属性。我们希望决策树的分支结点所包含的样本尽可能属于同一类别，即结点的“纯度”越来越高，可以高效地从根结点到达叶结点，得到决策结果。

## 结点“纯度”的度量
上面我们提到，构造决策树的关键是选择最优划分属性，选择纯度高的结点进行划分。这里给出了三种度量结点“纯度”的指标：
1. 信息增益
2. 增益率
3. 基尼指数

### 信息增益
首先了解信息熵的概念。假设样本集合D中第k类样本所占的比例为 p<sub>k</sub>(k=1,2,...,|y|)，则D的信息熵定义为：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-07/2019-07-11-3.png)

假设离散属性a有V个不同的取值，若使用a来对样本集D进行划分，则会产生V个分支节点，每个分支节点上的样本在a上的取值都相同。记第v个分支节点上样本为D<sup>v</sup> ，则可计算其相应的信息熵，再根据每个节点上的样本占比给分支节点赋予权重 D<sup>v</sup>/D ，可计算出以属性a作为划分属性所获得的“信息增益”为：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-07/2019-07-11-4.png)

信息增益越大，则意味着用属性a来进行划分所获得的"纯度提升"越大，因此，我们可用信息增益来进行决策树的划分属性选择。

### 增益率
使用信息增益方法进行属性划分，容易导致决策树不具备泛化能力。所以我们可以使用增益率来选择最优划分属性。如下图：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-07/2019-07-11-5.png)

其中，IV(a)称为a的“固有值”，a的可能取值数目越多，IV(a)的值通常越大。

我们还可以综合信息增益准则和信息率准则的特点：先从候选划分属性中找出信息增益高于平均水平的属性，再从中选择增益率最高的。

### 基尼指数
基尼值的计算如下：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-07/2019-07-11-6.png)

Gini反映了从数据集D中随机抽取两个样本，其类别标记不一样的概率，因此，Gini越小，则D的纯度越高。

属性a的基尼指数定义为：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-07/2019-07-11-7.png)

选择使上式最小的属性a作为划分属性。

## 剪枝处理
在生成决策树的过程中，为了避免过拟合和欠拟合的问题，我们需要对决策树进行适当的剪枝，剪枝方法有两种：

- 预剪枝：在决策树生成过程中，对每个结点在划分前先进行估计，若当前结点的划分不能带来决策树泛化性能提升，则停止划分并将当前结点标记为叶结点
- 后剪枝：先从训练集生成一棵完整的决策树，然后自底向上地对非叶结点进行考察，若将该结点对应的子树替换为叶结点能带来决策树泛化性能提升，则将该子树替换为叶结点。

两种方法的对比：
- 预剪枝使得决策树的很多分支都没有"展开”，不仅降低过拟合风险，而且显著减少训练/测试时间开销；但，有些分支的当前划分虽不能提升泛化性能，但在其基础上进行的后续划分却有可能导致性能显著提高，即预剪枝基于"贪心"本质禁止这些分支展开，给预剪枝决策树带来了欠拟含的风险。
- 后剪枝决策树通常比预剪枝决策树保留了更多的分支。一般情形下，后剪枝决策树的欠拟合风险很小，泛化性能往往优于预剪枝决策树，但后剪枝过程是在生成完全决策树之后进行的，并且要自底向上地对树中的所有非叶结点进行逐一考察，因此其训练时间开销比未剪枝决策树和预剪枝决策树都要大得多。

# 随机森林
随机森林指的是利用多棵决策树对样本进行训练并预测的一种分类器。随机森林的基本单元是决策树，每棵决策树都是一个分类器，多个决策树以随机的方式建立起的森林就是随机森林。对于一个输入样本，N棵树会有N个分类结果，而随机森林集成了所有的分类投票结果，将投票次数最多的类别指定为最终的输出。

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-07/2019-07-11-8.png)

随机森林里的每棵树按以下规则生成：如果训练集大小为N，对于每棵树而言，随机且有放回地从训练集中的抽取N个训练样本，作为该树的训练集，然后以该训练集构造一棵棵决策树。

得到许多决策树，便构成了一个随机森林，然后集成所有的分类树投票结果，将投票次数最多的类别指定为最终的输出。

# 并行化优化
决策树的构建本来就是很复杂，需要耗费很多时间的。所以我们可以通过并行化，充分利用计算机的硬件配置，加快决策树的构造速度。

并行化大致实现思路：使用multiprocessing创建4个进程来创建树，将总的树的数目平均分成4部分给各个进程分担；得到创建后树的列表之后，再创建4个进程，将测试数据的样本数平均分担给这4个进程。当使用multiprocessing时，用multiprocessing的queue来进行主进程与子进程之间的数据传递；用进程池Pool时用它自己的get()来获得子进程的结果。

具体步骤如下：
1. 要创建t棵树，创建4个进程，每个进程负责创建t/4棵决策树，创建好的t/4棵决策树以列表的形式返回到主进程。
2. 分别得到4个子进程的决策树列表之后，将4个子列表整合到一个长度为t的决策树列表L。
3. 创建4个分类进程，将决策树列表复制4份分别传递到4个分类进程，同时将测试数据分成4份，分别传递到4个分类子进程。
4. 每个进程以列表的形式返回分类结果。
5. 分别得到4个子进程的标签列表之后，将4个子列表整合到一个标签列表。该标签列表就是最终结果。

# cache友好
使用 python 实现随机森林算法并进行训练和预测时，消耗的内存将会非常地多。为了有效利用空间，我们应该把使用过后不会再使用的数据删除，代码如下：
```
del unUsedData
```

# 代码
```
import numpy as np
import pandas as pd
import xgboost as xgb
from sklearn import metrics
from sklearn.tree import DecisionTreeRegressor
from sklearn.ensemble import RandomForestRegressor

def myReadCsv(dataSet, path):
    # 文件没有列名，所以读取的时候设置 header = None，否则第一行数据无法读取
    data = pd.read_csv(path, header=None)
    dataSet = dataSet.append(data)
    del data
    return dataSet

trainData_X = pd.DataFrame()
trainData_Y = pd.DataFrame()
testData_X = pd.DataFrame()

trainData_X = myReadCsv(trainData_X, './data/train1.csv')
trainData_X = myReadCsv(trainData_X, './data/train2.csv')
trainData_X = myReadCsv(trainData_X, './data/train3.csv')
trainData_X = myReadCsv(trainData_X, './data/train4.csv')
trainData_X = myReadCsv(trainData_X, './data/train5.csv')

trainData_Y = myReadCsv(trainData_Y, './data/label1.csv')
trainData_Y = myReadCsv(trainData_Y, './data/label2.csv')
trainData_Y = myReadCsv(trainData_Y, './data/label3.csv')
trainData_Y = myReadCsv(trainData_Y, './data/label4.csv')
trainData_Y = myReadCsv(trainData_Y, './data/label5.csv')

testData_X = myReadCsv(testData_X, './data/test1.csv')
testData_X = myReadCsv(testData_X, './data/test2.csv')
testData_X = myReadCsv(testData_X, './data/test3.csv')
testData_X = myReadCsv(testData_X, './data/test4.csv')
testData_X = myReadCsv(testData_X, './data/test5.csv')
testData_X = myReadCsv(testData_X, './data/test6.csv')


'''
params = {
    'booster':'gbtree',
    #'objective':'reg:linear',    
    'stratified':True,
    'max_depth':5,                    # 构建树的深度，越大越容易过拟合
    'min_child_weight':1,
    'gamma':3,                      # 用于控制是否后剪枝的参数,越大越保守，一般0.1、0.2这样子
    'subsample':0.8,#0.7              # 随机采样训练样本
    'colsample_bytree':0.6,           # 生成树时进行的列采样
    'lambda':3,                       # 控制模型复杂度的权重值的L2正则化项参数，参数越大，模型越不容易过拟合
    'eta':0.05,                       # 如同学习率
    'seed':20,
    'silent':1,                       # 设置成1则没有运行信息输出，最好是设置为0.
    'eval_metric':'auc',
}

dtrain = xgb.DMatrix(trainData_X, label=trainData_Y)
dtest = xgb.DMatrix(testData_X)

num_round = 5000
model = xgb.train(params, dtrain, num_round, early_stopping_rounds=100)
preds = model.predict(dtest, ntree_limit=model.best_ntree_limit)

# 写入
result = pd.DataFrame()
result['id'] = list(range(1, 1 + len(preds)))
result['Predicted'] = preds
result.head()
result.to_csv('result.csv', index=0)
'''

 # XGBoost训练过程
model = xgb.XGBRegressor(max_depth=6, learning_rate=0.05, n_estimators=500, silent=False, objective='reg:squarederror', n_jobs=3)
model.fit(trainData_X, trainData_Y)

# 对测试集进行预测
preds = model.predict(testData_X)

# 写入
result = pd.DataFrame()
result['id'] = list(range(1, 1 + len(preds)))
result['Predicted'] = preds
result.head()
result.to_csv('result.csv', index=0)
```

# 参考文献
- [https://www.cnblogs.com/liuqing910/p/9121736.html](https://www.cnblogs.com/liuqing910/p/9121736.html)
- [http://www.mamicode.com/info-detail-2334080.html](http://www.mamicode.com/info-detail-2334080.html)
- [https://blog.csdn.net/chenyaxue/article/details/50791703](https://blog.csdn.net/chenyaxue/article/details/50791703)
- [https://baike.baidu.com/item/随机森林/1974765?fr=aladdin](https://baike.baidu.com/item/%E9%9A%8F%E6%9C%BA%E6%A3%AE%E6%9E%97/1974765?fr=aladdin)
- [https://baike.baidu.com/item/决策树/10377049?fr=aladdin](https://baike.baidu.com/item/%E5%86%B3%E7%AD%96%E6%A0%91/10377049?fr=aladdin)
