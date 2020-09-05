---
title: Kaggle竞赛指南 —— 主流机器学习算法
date: 2019-08-27 18:08:51
tags: [Kaggle]
categories: [Data&AI]
---
![](https://user-images.githubusercontent.com/1400357/91473128-58bdf480-e890-11ea-9db0-7339c430e9e0.png)


目前竞赛中（其实也是常规实际问题）的主流算法有四大门类：  Linear, Tree-based, kNN 以及 Neural Networks 下面分别简单介绍一下：

## Linear相关模型

假设我们有两组点， 灰色点属于一类，绿色点属于另一类，如上图所示， 那么我们可以很方便的拟合条直线将之区分为两类，落在右上方的为A 落在左下方的为B。
![](https://user-images.githubusercontent.com/1400357/91473847-5d36dd00-e891-11ea-93aa-8ce12b55e02c.png)


上述是两维的情况， 这种思路还可可以拓展到高维空间， 这个时候分界展现形式就可能是一个平面而非直线了， 具体算法比如Logistic Regression 以及SVM。

Linear相关模型非常适用于**稀疏高维数据**的分类。不过也有很多数据没有那么容易用Linear区分，比如下面这种“环”数据：

![](https://upload-images.jianshu.io/upload_images/1886630-ba74277e84f5ec61.png)

在应用过程中你不必从头实现Linear算法，各种现成的库可以在[Scikit-learn](https://scikit-learn.org/stable/)或者 [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki)找到， 后者专门用于处理大型数据集。

![](https://user-images.githubusercontent.com/1400357/91473985-8e171200-e891-11ea-873a-234e271098ac.png)


## Tree-based模型

Tree-based(决策树)模型通常是作为构建复杂模型的一个基础模型。类似于线性模型，我们假设有两组数据点，我们尝试用一条平行于坐标轴的直线将之分开

![](https://user-images.githubusercontent.com/1400357/91474073-aab34a00-e891-11ea-9945-5a4b4d09158f.png)

现在我们得到了两片区域，上面这片区域是灰色点的概率为1， 下面这片是灰色的概率为0.2 。 那么上面空间不用再拆分了，我们继续画一条直线拆分下面这片区域

![](https://user-images.githubusercontent.com/1400357/91474155-c1f23780-e891-11ea-80e6-5fb31d16a129.png)

这样我们得到左下区域的灰色概率为0， 剩余区域灰色概率为1。这个过程就是决策树工作的典型过程， 简单来讲就是使用分治的策略不断划分子空间直到不可再划分为止。

决策树算法的策略简单但是非常有效，其有两个最有名的变形分别是随机森林（Random Forest）和梯度提升决策树（Gradient Boost Decision Trees, GBDT）。决策树及其变形在Kaggle竞赛中应用极为广泛，不夸张的说几乎所有**表格数据**相关的竞赛其冠军方案都使用了这种策略。

不过决策树也不是万能的，在很多有明显线性依赖关系的数据集上，决策树表现要比线性模型要差很多，因为为了分割空间其需要生成大量树分岔，而且在决策边界也可能会变得不准确。

对于基本的决策树和Random Forest，我们依然可以在[Scikit-learn](https://scikit-learn.org/stable/)或者 [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki)中找到其实现，对于GBDT有两个主流的实现分别是 [XGBoost](https://xgboost.readthedocs.io/en/latest/)和[LightGBM](https://lightgbm.readthedocs.io/en/latest/)

## k-NN 

k-NN 是 k-Nearest 即K 邻近算法的缩写，我们还是从分类预测问题入手，类似的，我们希望预测下图中带问好的这个点的分类

![Screenshot from 2019-07-03 10-36-52.png](https://user-images.githubusercontent.com/1400357/91474592-52c91300-e892-11ea-8c84-7cf970136aff.png)

我们做一个假设， 即彼此接近的点可能会有相同的标签，因此我们希望能找到距离目标点相近的点，并把这个点的标签作为目标点的标签，因此某个点属于那个分类是由其邻居“投票”决定的。这个寻找过程就是邻近算法的基本思路。如果我们再拓展到k 个目标找其邻近的点，那么这个算法就会产出k个分类, 也就是k-NN算法。

尽管K-NN思路简单，但是却是竞赛中非常常用的一种前期处理思路。类似于前面两个模型，k-NN的实现也可以在[Scikit-learn](https://scikit-learn.org/stable/)或者 [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki)中找到。


## Neural Networks(NN)

NN是近几年耀眼的明星，这其实不是一个算法，而是一类特殊的机器学习模型，直观来讲，NN 类似于一个黑盒，与决策树不同，NN 可以生成一个平滑的分割曲线，在一些特定问题如图像、声音、文本和序列上，NN都取得了革命性的进步。NN的具体原理是需要专门系统学习的，但是你可以在 [https://playground.tensorflow.org/](https://playground.tensorflow.org/)获得一个直观的认识
![https://playground.tensorflow.org/](https://user-images.githubusercontent.com/1400357/91474744-886dfc00-e892-11ea-82f6-fadee9b563d1.png)
实现NN的软件包非常多，主流的有Tensorflow, Keras, Pytorch, Mxnet 。其中Keras是对Tensorflow的易用化封装， Pytorch近来由于其定义网络的灵活性受到越来越多资深Kaggler的青睐。

## One more thing

>Here is no method which outperforms all others for all tasks
没有银弹


没有一种方法可以适用于所有数据，每种方法都限定与其适用的数据类型假设。通常我们看到大多数竞赛题目上，GBDT 和 NN 方法都表现良好，但并不能因此低估其他比如Linear和K-NN方法，在某些问题上后者可能表现更好,比如这个结合了逻辑回归和随机森林算法的方案，得分超过了CNN获得了第一（[http://blog.kaggle.com/2017/03/24/leaf-classification-competition-1st-place-winners-interview-ivan-sosnovik/](http://blog.kaggle.com/2017/03/24/leaf-classification-competition-1st-place-winners-interview-ivan-sosnovik/)
）

