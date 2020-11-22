---
title: Kaggle竞赛指南 —— 简介
date: 2019-08-26 22:06:54
tags: [Kaggle]
categories: [ML]
---


![](https://user-images.githubusercontent.com/1400357/91358191-4c7f5c00-e7ea-11ea-8329-29a5ef4661d6.png)



## 一、为什么Kaggle

[Kaggle](https://www.kaggle.com/)是目前最大的数据科学竞赛与技能分享平台。在Kaggle上你可以查找和发布数据集，探索和构建模型，与其他数据科学家和机器学习工程师合作，并参加竞赛以解决数据科学挑战。参加Kaggle竞赛，你至少可以有以下几种收获：

1. 赢得奖金。奖金从1000-100000美金不等，只要能在参赛队伍中获取前三名即可分享。
2. 获取影响力以及offer。 比起有限的奖金来说，获得一个好排名似乎是大部分Kaggler的现实目标，一个好的Kaggle名次是入职各大公司的敲门金砖。
3. 学习经验。得益于数据科学家们对结果的极致追求，在Kaggle上你可以看到最前端的数据科学技巧，学习到比论文中更加实用的方法。


## 二、竞赛相关概念

![](https://user-images.githubusercontent.com/1400357/91358246-6325b300-e7ea-11ea-85d5-a5aabcb6306c.png)



### 1. 数据（Data）

这个板块上提供可下载的竞赛数据以及数据描述，小规模的数据可以在线预览。注意，有时你也可以用外部公开数据集来增强你的模型，但是要注意查看竞赛规则。

### 2. 模型（Model）
提起模型，我们脑中浮现的似乎是一个个算法，但实际上你应该更广泛的认识它，从广义上来说，模型是一种 **从数据到答案**的解决方法。 模型可能异常复杂，用到各种算法的叠加，各种特征处理方法（包括手工），以及各种工具。比如下面这个来自一次竞赛的冠军解决方案：
![](https://user-images.githubusercontent.com/1400357/91358305-7c2e6400-e7ea-11ea-9ad8-030ddb61f089.png)


会有一些Kaggler把他们的方案以Jupeter Notebook的形式发布在【Kernels】板块，这是一个学习各种先进思路的好地方（不局限于竞赛）。

### 3. 提交（Submission）

你需要将你的模型产出的预测结果提交给平台获取评分， 提交文件通常是类似于csv的文件：

![](https://user-images.githubusercontent.com/1400357/91358341-8fd9ca80-e7ea-11ea-8621-2a82224f8623.png)

### 4. 评估（Evaluation）

你需要了解一下常用的评估方法，比如：

* Accuracy
* Logistic Loss
* AUC
* RMSE
* MAE

### 5.排行榜(Leaderboard)
![](https://user-images.githubusercontent.com/1400357/91358383-a3853100-e7ea-11ea-97de-8e9d92f1fe5f.png)

关于排行榜，你需要了解的是，在竞赛截止前你所看到的竞赛分数是基于公开发布的数据进行评估的。竞赛截止后最终计分时会用未公开数据进行评估。所以有很多时候你看到在截止前一直排名前列的选手，最终计分时落差很大，那可能是他（她）在公共数据上过拟合了。

## 三、 硬件准备

可以解决大多数竞赛问题的配置（除去图像处理外）：
- 16G 内存
- 4 核CPU

更好一点的配置：
- 32 G 内存
- 6核CPU

几个关键概念：

内存： 如果你能把所有数据都装入内存，将极大提高处理效率
芯片核数： 越多核，就可以同时做越多的实验
存储： 如果处理大型数据集或者图片数据库，SSD 硬盘至关重要

当然你可以选择用各种云平台，常见的有
*  Amazon AWS
*  Microsoft Azure
*  Google Cloud

## 四、软件准备

语言：Python    
（前两年你可能还在纠结于R和Python之间，但现在请直接选Python)

技术栈

![](https://user-images.githubusercontent.com/1400357/91358410-b435a700-e7ea-11ea-9a7b-b5d570cdea22.png)

IDE

![](https://user-images.githubusercontent.com/1400357/91358441-c31c5980-e7ea-11ea-959c-a76799534598.png)

三方包

![](https://user-images.githubusercontent.com/1400357/91358478-d29ba280-e7ea-11ea-9fac-809c25c61c0b.png)

外部工具

![](https://user-images.githubusercontent.com/1400357/91358505-ddeece00-e7ea-11ea-8e75-46485c53d292.png)

关于软件安装，你可以用pip一个个安装，也可以直接使用Anaconda大礼包，里面已经包含了大部分常用工具。


# Next..

<!--  [Kaggle 参赛指南（二） —— 主流机器学习算法回顾 ](https://www.jianshu.com/p/13e5f72d47ef)

[Kaggle 参赛指南（三）-- 数据预处理](https://www.jianshu.com/p/d882c2dd6520)

[Kaggle参赛指南（四）—— 探索性数据分析](https://www.jianshu.com/p/41a871fb4a2b)
[Kaggle参赛指南（五）—— 问题求解套路](https://www.jianshu.com/p/f012d449e075) -->