---
title: 配对交易简介
date: 2020-11-21 08:24:12
tags: 'Quant'
categories: ['ML']
---

量化策略里有一大类叫做Pairs Trading-配对交易，是我觉得非常有趣的，其基本思路是寻找市场中表现相近的两个资产标的A和B, 当A涨得比较高的时候，卖出A买入B , 当A 跌的比较多的时候，买入A卖出B。这个思路很容易理解，因为如果两支股票确实相近，那么他们的价格有回归到相似的可能性，这也符合古老中国哲学里面“合久必分，分久必合”的朴素道理。

道理我们是都清楚的，甚至很多交易者也是模糊的按照这个思路来做的，比如当看到百事股价跟可口可乐股价偏离太远时，可能会考虑购进一些百事股票，卖出可口股票。问题是如何把这些模糊的想法进行量化分析，进而让算法捕捉到你看不到的机会，这主要需要解决两个问题：1. 如何发现符合要求的配对 2. 何时进场买卖 。 下面我们以[QUANTAXIS](https://github.com/QUANTAXIS/QUANTAXIS)为回测工具，以沪深市场为例来简要介绍下一个基本的配对交易策略是怎么进行的。

## 发现配对

发现配对的过程在量化角度来说是确定两支股票在一段时间的价格走势是否符合统计学的相关性, 即Cointegration test(协整检验)。

我们用Python代码来说明一下这个过程，这里用到了statsmodels.tsa.stattools 库中的coint方法，该方法是基于增强的Engle-Granger两步检验。

```python
def integration_test(x, y):
    result = coint(list(x), list(y))
    score = result[0]
    pvalue = result[1]
    if pvalue < 0.05:
        return True
    return False
```

其中x 和 y 为两支股票同一个时间区间的价格序列, 其假设结果是非相关，如果pvalue小于我们设置的显著性水平，则拒绝假设，两者相关。

据此方法，我们可以搜寻预设的股票列表code_list, 这里采用了QUANTAXIS的方法QA_fetch_stock_day_adv获取日线数据

```Python
def find_pairs(code_list, start_date, end_date, method_type):
    n = len(code_list)
    pairs = []
    for i in range(n):
        for j in range(i+1, n):
            c1 = code_list[i]
            c2 = code_list[j]
            S1 = QA.QA_fetch_stock_day_adv(c1, start_date, end_date).to_qfq().close
            S2 = QA.QA_fetch_stock_day_adv(c2, start_date, end_date).to_qfq().close
            if len(S1)!= len(S2):
                continue
            if methods[method_type](S1, S2):
                pairs.append((c1, c2))
    return pairs
```

假设我们锁定在新能源板块，时间区间设定在2019-10-01到2020-10-01那么可获得股票池代码列表

`code_list= QA.QA_fetch_stock_block_adv().get_block('新能源').code`

应用find_pairs 方法可以得到如下股票 配对

```
[('002805', '300484'),
 ('002805', '600847'),
 ('002805', '300037'),
 ('002805', '600884'),
 ('600847', '300037'),
 ('600847', '300450'),
 ('600847', '600884'),
 ('002407', '600884')]
```

我们把第一个配对的价格走势放在一起看下

![image-20201121115437917](https://user-images.githubusercontent.com/1400357/99885088-6a622e80-2c2a-11eb-969a-4d65b3bc9d95.png)

那么还是可以看到有一些相关性

然后我们可以对这两支股票价格进行标准化，即计算一下这两支股票每日价格的比率ratios

```Python
def get_ratios(code_x, code_y):
    x = QA.QA_fetch_stock_day_adv(code_x, start_date, end_date).to_qfq().close
    y = QA.QA_fetch_stock_day_adv(code_y, start_date, end_date).to_qfq().close
    x.index = y.index
    return x.div(y, axis = 'index')
```

```
def plot_zscore(series):  
    zscores = (series - series.mean()) / np.std(series)
    zscores.plot()
    plt.axhline(zscores.mean())
    plt.axhline(1.0, color='red')
    plt.axhline(-1.0, color='green')
    plt.show()
```

![image-20201121115704035](https://user-images.githubusercontent.com/1400357/99885090-6afac500-2c2a-11eb-85ae-cfb44d034249.png)

这张图可以看到这两支股票价格的比率(ratios)大部分时间是在[-1, 1]区间内。那么接下来我们可以基于这个比率实现我们的交易策略，注意到此时其实相当于我们已经将两支股票的价格拟合为一个价格曲线。

## 执行交易

那么我们先实现一个最简单的策略，比率>1的交易日子，卖出code1买入code2, 比率<-1的交易日子，卖出code2买入code1

```Python
def pairs_backtest(code1, code2):
    for i,items in enumerate(DATA.panel_gen):
        item1 = items.select_code(code1)
        item2 = items.select_code(code2)
        order_time = items.date[0]   
        if ratios[i] > 1:             
            place_order(code2,order_time, 1000, QA.ORDER_DIRECTION.BUY,item2)
            if AC.sell_available.get(item1.code[0], 0)>0:
                place_order(code1,order_time, 1000, QA.ORDER_DIRECTION.SELL,item1)   
        if ratios[i] < -1:
            if AC.sell_available.get(item2.code[0], 0)>0:
                place_order(code2,order_time, 1000, QA.ORDER_DIRECTION.SELL,item2)
            place_order(code1,order_time, 1000, QA.ORDER_DIRECTION.BUY,item1)     
        AC.settle()
    AC.save()
```

利用QUANTAXIS提供的风险分析工具QA_Risk， 以长期持有CS300指数基金作为对比，可以得到这个策略的表现为

![image-20201121180655010](https://user-images.githubusercontent.com/1400357/99885091-6b935b80-2c2a-11eb-9a6d-8587dfb186a0.png)

肉眼可见的大幅跑输CS300，其中几个关键指标 profit = -11.0%  sharpe = -0.36  max_dropbac = 0.13  

那么这个策略就此宣告失败了么？先别急着下结论，我们这里只是机械的根据ratio的绝对值来判断下单，如果我们参照广泛应用在个股均值回归策略，计算ratio均值的momentum，在短期的ratio变化趋势走强或走弱时再下单，是否结果会好一些？我们来试一下

这需要我们先要计算ratio的趋势变化指标

```python
def calc_zscore(ratios, window1, window2):
    ma1 = ratios.rolling(window=window1,
                       center=False).mean()
    ma2 = ratios.rolling(window=window2,
                           center=False).mean()
    std = ratios.rolling(window=window2,
                    center=False).std()
    zscore = (ma1 - ma2)/std
    return zscore
```

如果我们设置window1=5 , window2=30 连同之前计算得到的ratios 代入上面方程，可以得到一个zscore序列，代表5日均线与30日均线ratio值的标准差

然后让我们将交易指标由ratios >1 或 ratios <-1 替换为 zscore > 1 或zscore <-1

再次进行交易回测，可以得到新策略的表现为

![image-20201121181951189](https://user-images.githubusercontent.com/1400357/99885092-6c2bf200-2c2a-11eb-84d5-63ae3484d822.png)

这时的回报率可见就好很多，关键指标profit=15% shape = 0.11 max_dropback = 0.21

可见虽然相比基准指数年华回报率的18% 还是没有赶上，但这起码是个整体盈利的策略。

## 写在最后

本文介绍了配对交易的基本流程，在实际交易过程中还有很多细节问题需要深入研究与优化，包括但不限于

* 除去Engle-Granger方法的其他配对相关性的统计计算指标
* 应用Kalman-filter 方法判断配对交易的趋势入场点
* 交易下单的资金仓位分配策略（本文简化为每次相同的1000元）
* 其他应用最新的机器学习和深度学习方法在处理高频数据发掘配对以及判断交易入场点

后续我会Post对其中一些方法的研究

另衷心感谢天神@yutiansut开源的QUANTAXIS框架