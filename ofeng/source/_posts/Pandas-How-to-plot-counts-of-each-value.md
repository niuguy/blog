---
title: '[Pandas]How to plot counts of each value'
date: 2018-10-22 20:40:47
categories: 'ML'
tags: 'Pandas'
---

1. With pandas build in function(actually matplotlib)

```Python
import pandas as pd
% matplotlib inline
col_values = ('x', 'x', 'y', 'y' , 'y', 'z')
df = pd.DataFrame({'col':col_values})
df['col'].value_counts().plot.bar()
```

![xyz](https://raw.githubusercontent.com/niuguy/blog/master/public/pic/xyz.png)

2. With seaborn

```Python
import seaborn as sns
sns.countplot(df['col'])
```

![xyz](https://raw.githubusercontent.com/niuguy/blog/master/public/pic/xyz2.png)