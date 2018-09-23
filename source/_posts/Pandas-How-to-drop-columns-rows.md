---
title: '[Pandas]How to drop columns/rows'
date: 2018-09-18 21:39:25
categories: 'pandas'
---

```python
import pandas as pd
import numpy as np

df = pd.DataFrame({'A#':[1,2,3], 'B#':[4,5,6], 'C#':[7,8,9]})
df
```

| A#   | B#   | C#   |
| ---- | ---- | ---- |
| 1    | 4    | 7    |
| 2    | 5    | 8    |
| 3    | 6    | 9    |

### Drop columns

```python
df.drop(['B#', 'C#'], axis = 1)
#or
df.drop(columns=['B#', 'C#'])
```

| A#   |
| ---- |
| 1    |
| 2    |
| 3    |

### Drop rows

```python
df.drop([0,1])
```

| A#   | B#   | C#   |
| ---- | ---- | ---- |
| 3    | 6    | 9    |