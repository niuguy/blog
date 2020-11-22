---
title: Kaggle竞赛指南 —— 问题求解套路
date: 2019-11-28 07:26:52
tags: [Kaggle]
categories: [ML]
---

顶级的Kaggler都有自己的一套问题解决流程，熟悉这些套路能极大提高求解问题的效率。今天我们来看一下目前Kaggle总排名第四的[kazanova](https://www.kaggle.com/kazanova)总结出来的一套解题套路。
具体如下：

![](https://user-images.githubusercontent.com/1400357/91630580-595aa600-e9ca-11ea-9dbb-aeacfebf956c.png)

下面分别看一下各阶段主要做什么事情：

1. 理解问题

*  这是个什么问题。分类还是回归，预测还是优化，需要有一个直观理解。
*  数据特征。图像数据，文本数据还是声音数据，数据集是表格数据么，是否是与时间相关的。
*  数据规模。从而可能需要准备什么样的软硬件，需不需要用GPU， 大概需要用多少内存以及硬盘。

2. 探索性数据分析

* 画出每个变量的直方图，看一下训练集及测试集的分布相似程度， 
* 画出特征变量相对于目标变量的关系图，特征变量相对于时间的变化图（如果是时间序列问题）
通过看数据分布可以导出相应的交叉验证策略。

3. 制定交叉验证（CV）策略
在对数据充分理解基础上，要确定验证策略，这一步非常重要，实际上很多排名靠前的方案最主要就是因为找到了正确的CV策略。可以从以下几点思考：
* 时间是否是个重要的变量。如果是需要根据时间做训练集测试集的分割，且要注意要用历史数据来预测未来数据。
* 如果数据分布是分层的，是否要考虑用分层验证（Stratified Validation）
* 如果数据是随机分布的，是否考虑采用随机验证，如随机K折验证

4. 特征工程
特征工程做的好坏在大多数竞赛问题中起到了决定性的作用，面对不同问题需要采取不同策略，具体思考方向如下。
* 图像识别：考虑正规化（scaling），平移(shifting)，翻转(rotation)以及CNN。 参考[https://www.kaggle.com/c/data-science-bowl-2018](https://www.kaggle.com/c/data-science-bowl-2018)
* 声音识别：傅立叶变换（Fourier），[mfcc](https://en.wikipedia.org/wiki/Mel-frequency_cepstrum), 波普分析（specgram），正规化。 参考[https://www.kaggle.com/c/tensorflow-speech-recognition-challenge](https://www.kaggle.com/c/tensorflow-speech-recognition-challenge)
* 文本分类：Tf-idf, svd, Stemming, 拼写检查（spell checking）, stop words' removal, x-grams 。参考[https://www.kaggle.com/c/stumbleupon/overview](https://www.kaggle.com/c/stumbleupon/overview)
* 时间序列： 时间延迟（Lags）,加权平均（weighted average）, 指数平滑（expotional smoothing） 。参考[https://www.kaggle.com/c/walmart-recruiting-store-sales-forecasting](https://www.kaggle.com/c/walmart-recruiting-store-sales-forecasting)
* 类别数据：目标特征，频率，one-hot， 顺序，label encoding。 参考[https://www.kaggle.com/c/amazon-employee-access-challenge](https://www.kaggle.com/c/amazon-employee-access-challenge)
* 数字数据：正规化，绑定（binning）, 求导（derivatives），离群值（outlier remove）,降纬。参考
[https://www.kaggle.com/c/afsis-soil-properties/overview](https://www.kaggle.com/c/afsis-soil-properties/overview)
* 交互数据：相乘，相除，相加，groupby。 参照[https://www.kaggle.com/c/homesite-quote-conversion](https://www.kaggle.com/c/homesite-quote-conversion)
* 推荐系统: 历史交易数据，货品流行程度，购买频度。参考[https://www.kaggle.com/c/acquire-valued-shoppers-challenge](https://www.kaggle.com/c/acquire-valued-shoppers-challenge)

5. 方法建模
类似于特征工程， 根据不同问题有不同的建模策略

* 图像识别：CNN(ResNet, VGG)
* 声音识别：CNNs(CRNN), LSTM
* 文本分类：GBM, Linear, DL, Naive Bayes, KNNs, LibFM, Libffm
* 时间序列： Augtoregressive, ARIMA, Linear, GBMs, DL, LSTMs
* 类别数据：GBMs, Linear, DL, LibFM, Libffm
* 数字数据：GBMs, Linear, DL, SVMs
* 交互数据：GBMs, Linear, DL
* 推荐系统: CF, DL, LibFM, Libffm, GBMs

应用中每种方法独立运行评估， 在不同的数据集上评估，可考虑做bagging.


6. 集成学习

这个阶段邻近竞赛结束，此时已经保存了基于不同方法的预测结果，可根据这些结果做有机整合争取最优化预测模型并提交。有多种集成学习策略，比如针对小数据集可以考虑简单的平均化（averaging）， 对于大规模数据集可以考虑做stacking。
