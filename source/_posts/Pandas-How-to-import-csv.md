---
title: '[Pandas]How to import CSV'
date: 2018-09-16 16:30:37
tags:
categories: 'pandas'
---


### Locate the file
There are two ways to locate the csv file, "absolute path" or "relative path"
e.g.
Absolute path:
"`/work/project/data/great.csv`"(Mac) or "`C:\work\project\data\great.csv`"(Windows)
Relative path:
"`data/greate.csv`"(Mac) or "`data\great.csv`"(Windows)

### Read with pandas.read_csv()
The result is a dataframe Object


```python
import pandas as pd
import numpy as np 

df = pd.read_csv('data/great.csv')
df
```

|company|locate|employees|avenue|
|--- |--- |--- |--- |
|orange|New York|10000|4000.0|
|banana|London|2000|1000.0|
|pinch|Paris|4000|5000.0|
|pear|Berlin|3000|NaN|




### No infered header


```python
df = pd.read_csv('data/great.csv', header= None)
df
```

|0|1|2|3|
|--- |--- |--- |--- |
|company|locate|employees|avenue|
|orange|New York|10000|4000|
|banana|London|2000|1000|
|pinch|Paris|4000|5000|
|pear|Berlin|3000|NaN|



###  Filter rows 


```python
df = pd.read_csv('data/great.csv', skiprows=[3,4])
df
```

|company|locate|employees|avenue|
|--- |--- |--- |--- |
|orange|New York|10000|4000|
|banana|London|2000|1000|


lambda is also welcomed


```python
df = pd.read_csv('data/great.csv', skiprows=lambda x:x/2==1)
df
```

|company|locate|employees|avenue|
|--- |--- |--- |--- |
|orange|New York|10000|4000.0|
|pear|Berlin|3000|NaN|


### Filter columns(or keep wanted)


```python
df = pd.read_csv('data/great.csv', usecols=['company'])
df
```

|company|
|--- |
|orange|
|banana|
|pinch|
|pear|


Other options refer https://pandas.pydata.org/pandas-docs/stable/generated/pandas.read_csv.html
