---
title: '[Pandas]How to rename columns'
date: 2018-09-17 22:11:55
categories: 'pandas'
---

```python
import pandas as pd
import numpy as np

df = pd.DataFrame({'A#':[1,2], 'B#':[3,4], 'C#':[5,6]})
df
```

| A#   | B#   | C#   |
| ---- | ---- | ---- |
| 1    | 3    | 5    |
| 2    | 4    | 6    |

**Rename with DataFrame.rename()**

```python
df.rename(index= str, columns={"A#": "a", "B#": "b"})
```

| a    | b    | C#   |
| ---- | ---- | ---- |
| 1    | 3    | 5    |
| 2    | 4    | 6    |

Print df again

| A#   | B#   | C#   |
| ---- | ---- | ---- |
| 1    | 3    | 5    |
| 2    | 4    | 6    |

Column names did'nt change. 

**To change names permanently, use  "inplace=True"**

```python
df.rename(index= str, columns={"A#": "a", "B#": "b"}, inplace = True)
df
```

| a    | b    | C#   |
| ---- | ---- | ---- |
| 1    | 3    | 5    |
| 2    | 4    | 6    |

**Rename in batch**

```python
df.rename(index= str, columns=lambda x:x.replace('#',''))
```

| A    | B    | C    |
| ---- | ---- | ---- |
| 1    | 3    | 5    |
| 2    | 4    | 6    |

Lambda is amazing