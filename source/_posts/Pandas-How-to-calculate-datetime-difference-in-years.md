---
title: '[Pandas]How to calculate datetime difference in years'
date: 2018-10-22 16:43:34
categories: 'Pandas'
---

Suppose you got a person's regist time in one column and his birth date in another column, now you need to calculate his age when he did the registration. There are two ways to reach this result.

1. With the help of relativedelta

```Python
from dateutil import relativedelta
for i in df.index:
    df.loc[i, 'age'] = relativedelta.relativedelta(df.loc[i, 'regDate'], df.loc[i, 'DateOfBirth']).years
   
```



2. With the help of np.timedelta64

```Python
import numpy as np

df['age'] = (df['regDate'] - df['DateOfBirth'])/np.timedelta64(1, 'Y')
# You may need to convert to integer
df['age'].apply(np.int64)
```

