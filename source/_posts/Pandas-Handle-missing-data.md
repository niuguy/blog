---
title: '[Pandas]Handle missing data'
date: 2018-09-23 20:40:37
categories: 'pandas'
---

Missing data is a common problem in real data preprocessing, luckily pandas has done a lot to help us handle it. This article will show the codes on how to do it.

###Produce some data with missing values.

```python
import pandas as pd
import numpy as np

df = pd.DataFrame(np.random.randn(5, 3), index=['a', 'c', 'e', 'f', 'h'],
                 columns=['one', 'two', 'three'])
df['four']='bar'
df['five']= df['one'] > 0
df['date']= pd.Timestamp('2018-01-01')
df2 = df.reindex(['a','b', 'c','d', 'e', 'f','g', 'h'])
df2
```

|      | one       | two       | three     | four | five  | date       |
| ---- | --------- | --------- | --------- | ---- | ----- | ---------- |
| a    | -0.614301 | 0.628725  | -0.903163 | bar  | False | 2018-01-01 |
| b    | NaN       | NaN       | NaN       | NaN  | NaN   | NaT        |
| c    | -0.297019 | 1.357266  | -0.665749 | bar  | False | 2018-01-01 |
| d    | NaN       | NaN       | NaN       | NaN  | NaN   | NaT        |
| e    | 0.311524  | -0.328388 | 0.777467  | bar  | True  | 2018-01-01 |
| f    | 0.572373  | -0.309563 | -0.296276 | bar  | True  | 2018-01-01 |
| g    | NaN       | NaN       | NaN       | NaN  | NaN   | NaT        |
| h    | -1.303842 | 1.239911  | -0.909255 | bar  | False | 2018-01-01 |

Notice that NaN is the default marker of missing number, text or boolean, NaT is for missing datetime

### Detect missing value

pandas provides two methods, isna() and notna()

```python
df2.isna()
```

|      | one   | two   | three | four  | five  | date  |
| ---- | ----- | ----- | ----- | ----- | ----- | ----- |
| a    | False | False | False | False | False | False |
| b    | True  | True  | True  | True  | True  | True  |
| c    | False | False | False | False | False | False |
| d    | True  | True  | True  | True  | True  | True  |
| e    | False | False | False | False | False | False |
| f    | False | False | False | False | False | False |
| g    | True  | True  | True  | True  | True  | True  |
| h    | False | False | False | False | False | False |

```python
df2.notna()
```

|      | one   | two   | three | four  | five  | date  |
| ---- | ----- | ----- | ----- | ----- | ----- | ----- |
| a    | True  | True  | True  | True  | True  | True  |
| b    | False | False | False | False | False | False |
| c    | True  | True  | True  | True  | True  | True  |
| d    | False | False | False | False | False | False |
| e    | True  | True  | True  | True  | True  | True  |
| f    | True  | True  | True  | True  | True  | True  |
| g    | False | False | False | False | False | False |
| h    | True  | True  | True  | True  | True  | True  |

### Cleaning

drop na value, dropna() has a parameter axis which indicates either colums(axis = 1) or rows(axis = 0) will be dropped, the default value of axis is 0

```python
df2.dropna()
```

|      | one       | two       | three     | four | five  | date       |
| ---- | --------- | --------- | --------- | ---- | ----- | ---------- |
| a    | -0.614301 | 0.628725  | -0.903163 | bar  | False | 2018-01-01 |
| c    | -0.297019 | 1.357266  | -0.665749 | bar  | False | 2018-01-01 |
| e    | 0.311524  | -0.328388 | 0.777467  | bar  | True  | 2018-01-01 |
| f    | 0.572373  | -0.309563 | -0.296276 | bar  | True  | 2018-01-01 |
| h    | -1.303842 | 1.239911  | -0.909255 | bar  | False | 2018-01-01 |

Drop columns with missing data

```python
df2.dropna(axis = 1)
```

|      |
| ---- |
| a    |
| b    |
| c    |
| d    |
| e    |
| f    |
| g    |
| h    |

All columns are removed..

### Fill na

Na value can be easily filled by fillna()

```python
df2.fillna(0)
```

|      | one       | two       | three     | four | five  | date                |
| ---- | --------- | --------- | --------- | ---- | ----- | ------------------- |
| a    | -0.614301 | 0.628725  | -0.903163 | bar  | False | 2018-01-01 00:00:00 |
| b    | 0.000000  | 0.000000  | 0.000000  | 0    | 0     | 0                   |
| c    | -0.297019 | 1.357266  | -0.665749 | bar  | False | 2018-01-01 00:00:00 |
| d    | 0.000000  | 0.000000  | 0.000000  | 0    | 0     | 0                   |
| e    | 0.311524  | -0.328388 | 0.777467  | bar  | True  | 2018-01-01 00:00:00 |
| f    | 0.572373  | -0.309563 | -0.296276 | bar  | True  | 2018-01-01 00:00:00 |
| g    | 0.000000  | 0.000000  | 0.000000  | 0    | 0     | 0                   |
| h    | -1.303842 | 1.239911  | -0.909255 | bar  | False | 2018-01-01 00:00:00 |

We can also fill data with respect to their column property

```python
df2.fillna(value = {'one': 0, 'four': 'missing', 'five': False, 'date': pd.Timestamp('2000-01-01')})
```

|      | one       | two       | three     | four    | five  | date       |
| ---- | --------- | --------- | --------- | ------- | ----- | ---------- |
| a    | -0.614301 | 0.628725  | -0.903163 | bar     | False | 2018-01-01 |
| b    | 0.000000  | NaN       | NaN       | missing | False | 2000-01-01 |
| c    | -0.297019 | 1.357266  | -0.665749 | bar     | False | 2018-01-01 |
| d    | 0.000000  | NaN       | NaN       | missing | False | 2000-01-01 |
| e    | 0.311524  | -0.328388 | 0.777467  | bar     | True  | 2018-01-01 |
| f    | 0.572373  | -0.309563 | -0.296276 | bar     | True  | 2018-01-01 |
| g    | 0.000000  | NaN       | NaN       | missing | False | 2000-01-01 |
| h    | -1.303842 | 1.239911  | -0.909255 | bar     | False | 2018-01-01 |

Remind that fillna() will not replace na in orignial dataframe, in other words, the df2 remains the same after all the above operation, If you want it to be permanantely changed, add parameter inplace = True like this.

```python
df2.fillna(value = 0, inplace = True)
```

